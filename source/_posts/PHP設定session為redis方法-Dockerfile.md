---
title: PHP設定session為redis方法(Dockerfile)
date: 2019-04-13 19:55:55
tags: [php, redis, session]
categories: PHP
---

最近 PHP 要設定 redis
不過這一快真的不是很熟
所以 GOOGLE 有找一下設定方法
這邊整理

<!--more-->

## PHP session 吃 redis (Dockerfile)

```dockerfile
FROM php:7.1-fpm

RUN apt-get update && \
  apt-get install unzip wget -y && \
  cd /tmp && \
  wget https://github.com/phpredis/phpredis/archive/master.zip -O phpredis.zip && \
  unzip -o /tmp/phpredis.zip && \
  mv /tmp/phpredis-* /tmp/phpredis && \
  cd /tmp/phpredis && \
  phpize && \
  ./configure && \
  make && \
  make install && \
  mkdir -p /etc/php5/mods-available/ && \
  touch /etc/php5/mods-available/redis.ini && \
  echo extension=redis.so > /etc/php5/mods-available/redis.ini && \
  ln -s /etc/php5/mods-available/redis.ini /usr/local/etc/php/conf.d && \
  echo "session.save_handler = redis\nsession.save_path = tcp://redis/6379" >> /usr/local/etc/php/conf.d/docker-php-ext-redis.ini && \
  apt-get remove unzip wget -y && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/

RUN usermod -u 1000 www-data && \
  chown -R www-data:www-data /var/www/html/

WORKDIR /var/www/html
VOLUME /var/www/html

EXPOSE 9000
```

[php-redis-session/Dockerfile at master · DawTaylor/php-redis-session](https://github.com/DawTaylor/php-redis-session/blob/master/php-7.1/Dockerfile)

這邊可能要注意
這個範例只有包 php-fpm(沒有 redis)
`tcp://redis/6379`這一段 redis 需要照你的環境設定調整

```bash
## redis 架設
docker run --name redis -d redis
## php-redis
docker run -d -p 80:80  --rm --name my-app -v  `pwd`:/var/www/html --link redis  dawtaylor/php-redis-session:5.6-apache
```

[DawTaylor/php-redis-session](https://github.com/DawTaylor/php-redis-session)

文件有簡單說明，但沒有作 link

測試使用方法可以寫一個 phpinfo 去看設定

或者寫一個測試 php session

```php
<?php
session_start();
$_SESSION['test'] = 2;
var_dump($_SESSION);
```

```bash
## redis cli 指令，可以進去container打redis-cli進入
keys *
## 查看某一個redis資料
get PHPREDIS_SESSION:b234200eb3afc2803630be437b177bd0
```

[KEYS – Redis](https://redis.io/commands/KEYS)

### php.ini 設定

> 安裝 php-pecl-redis：
>
> ```
> yum install php-pecl-redis
> ```
>
> 修改 PHP 設定檔：
>
> ```
> session.save_handler = redis
> session.save_path = "tcp://127.0.0.1:6379?auth=密碼"
> ```
>
> 其中 session.save_path 的格式為
> session.save_path = "tcp://host1:6379?weight=1, tcp://host2:6379?weight=2&timeout=2.5, tcp://host3:6379?weight=2&read_timeout=2.5"
> 可設定的參數有：weight、timeout、persistent、prefix(預設"PHPREDIS_SESSION:")、auth、database...

[XYZ 的筆記本: PHP 使用 Redis 儲存 Session](https://xyz.cinc.biz/2018/03/php-redis-session.html)

## redis cluster

為什麼設定 redis cluster 要設定多個 IP?
這原本是我第一個疑問，但是網路上都沒有看到有人講這個
最後猜測是其實連其中一台，就可以做了
但是設定那台掛掉就連到 redis
不過我很好奇，怎麼不會用 haproxy 或 dns 去做設定??

### redis cluster 簡單介紹

(PS:這邊有參雜我自己的想法)

因為目前沒有自己架設 redis cluster，所以我就不先研究那一快
畢竟我連 redis 架設可能不是很熟
但我覺得還是要了解他怎麼運作
一直設定 redis 連線設定，但我覺得還是要了解一下

簡單我目前看到的認知 redis 有 master→slave (slave→slave)
這跟 mysql 一樣，有做備份的解決方案
在 redis 3.0 之前沒有 cluster 功能
所以會看到 sentinel 這個東西(目前我網路上看到的了解)
這個有點像是會監測 master 有沒有掛掉
假如掛掉的話，他會把 slave 調成 master
這樣你程式掛掉還是可以使用
cluster 也是有做這個事情
網路有看到 sentinel 沒辦法馬上做到 redis 掛掉那瞬間第一毫秒
所以有大量資料近來，有風險程式會沒接收到
假如沒寫好 try...catch 就會遺失資料

簡單來說，redis cluster 正常來說會有六台
3 台 master3 台 slave，1 台 master 對應各 1 個 slave
3 個 master 做 cluster，當有一台 master 掛掉 slave 會變成 master
維持線上服務

> 為什麼設定 redis cluster 要設定多個 IP?
> 這原本是我第一個疑問，但是網路上都沒有看到有人講這個
> 最後猜測是其實連其中一台，就可以做了
> 但是設定那台掛掉就連到 redis
> 不過我很好奇，怎麼不會用 haproxy 或 dns 去做設定??

這邊我做一個假說，redis cluster 程式做多組 IP 是為了當第一台掛掉
可能連第二台 IP，所以這邊會放多組原因，就算連到的話。redis cluster 也會分配其中一台
那為什麼不做 haproxy，設定就不會那麼麻煩

> 基于代理的分片
> ...
> 代理层多了一次转发，性能有所损耗。

[Redis Cluster 深入与实践（续） - 掘金](https://juejin.im/post/5a54a6fbf265da3e3f4c9048) {% asset_link 1.png 備份圖 %}

看來設定多組有這個原因，所以才不用 haproxy，但不知道為什麼沒設定 DNS 呢?
DNS 導向也會影響?
這個問題我還沒想到

### phpredis,predis

網路上有看到這兩個工具
簡來來說 phpredis 是透過 C 源生連線
predis 是透過 socket 去做連線
可能上方效能會比較好
predis 不需要安裝 redis 連線工具

[在 php 中使用 redis cluster 集群 - 作业部落 Cmd Markdown 编辑阅读器](https://www.zybuluo.com/phper/note/248555)

> predis，是 PHP 版本寫的 redis client，採用 socket 連接
> php extension redis 是 PHP 原生擴展，C 寫的
> 由於沒有進行過大數據壓測，不能準確告訴你性能差異。但基本上擴展 redis 肯定比 predis 更好。
> Laravel 推薦用 predis，主要是當心一些主機沒有支持 redis 吧。儘量少依賴 C 擴展，這樣才能發揮 PHP 普及眾生的思想。

[predis 包和 phpredis 扩展的区别是什么 - SegmentFault 思否](https://segmentfault.com/q/1010000008848852)

### 有關 docker 架設資源

[Grokzen/docker-redis-cluster: Dockerfile for Redis Cluster (redis 3.0+)](https://github.com/Grokzen/docker-redis-cluster)
[使用 docker 建立 Redis Cluster - 更新版 - Yowko's Notes](https://blog.yowko.com/redis-cluster-docker/)

基於設定我就不多研究了
我看不懂裡面設定
先暫時留在這邊

相關來源:

- [在 php 中使用 redis cluster 集群 - 作业部落 Cmd Markdown 编辑阅读器](https://www.zybuluo.com/phper/note/248555)
- [predis 包和 phpredis 擴展的區別是什麼 - SegmentFault 思否](https://segmentfault.com/q/1010000008848852)
- [phpredis Redis 集群 Redis Cluster - zh7314 - 博客园](https://www.cnblogs.com/zx-admin/p/7750612.html)
- [面试中关于 Redis 的问题看这篇就够了 - 掘金](https://juejin.im/post/5ad6e4066fb9a028d82c4b66)
- [一文輕鬆搞懂 redis 集群原理及搭建與使用 - 掘金](https://juejin.im/post/5ad54d76f265da23970759d3)
- [php-redis-session/php-7.1 at master · DawTaylor/php-redis-session](https://github.com/DawTaylor/php-redis-session/tree/master/php-7.1)
- [php 如何通过 pecl 安装 redis 扩展](https://newsn.net/say/pecl-install-redis.html)
- [深入浅出 Docker 技术- Redis sentinel 集群的搭建 – VieMall](http://www.dczou.com/viemall/837.html)
- [Redis Cluster 深入与实践 | Aoho's Blog](http://blueskykong.com/2017/09/29/rediscluster/)
- [Redis Cluster 深入与实践（续） - 掘金](https://juejin.im/post/5a54a6fbf265da3e3f4c9048)
- [使用 docker 建立 Redis Cluster - 更新版 - Yowko's Notes](https://blog.yowko.com/redis-cluster-docker/)
- [XYZ 的筆記本: PHP 使用 Redis 儲存 Session](https://xyz.cinc.biz/2018/03/php-redis-session.html)

**2019-07-10**

最近公司要用 redis cluster
所以 Google 一下設定

- [phpredis/cluster.markdown at develop · phpredis/phpredis](https://github.com/phpredis/phpredis/blob/develop/cluster.markdown#session-handler)
- [XYZ 的筆記本: PHP 使用 Redis 儲存 Session](https://xyz.cinc.biz/2018/03/php-redis-session.html)

這個設定要安裝 pecl-php-redis

```
session.save_handler = rediscluster
session.save_path = "seed[]=host1:port1&seed[]=host2:port2&seed[]=hostN:portN&timeout=2&read_timeout=2&failover=error&persistent=1&auth=password"
```

設定沒有想像中麻煩

save_path 可用 array_map 和 join 去組出字串
