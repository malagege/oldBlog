---
title: Loki 初體驗教學(在Raspberry 啟用 Docker-Loki-Driver)
date: 2021-04-18 02:19:32
tags: [loki, traefik ,docker, log, grafana]
categories: Loki
---

最近在三台樹梅派使用 Docker 架設服務
但是查看 log 真的不是很方便
最近剛完 Prometheus 看到 Loki
跟 Prometheus 很像，又是跟 Container 很有關係
看了一下佈署流程，並不是非常難
所以就食做看看

<!--more-->

使用官方 docker-compose [loki/docker-compose.yaml at master · grafana/loki · GitHub](https://github.com/grafana/loki/blob/master/production/docker-compose.yaml)
grafana 帳號密碼為 admin/admin
進去新加 datasource 為 loki
這樣就算部屬完成

Grafana 要點選 `expore` 才能看到日誌

```yaml=
version: "3"

networks:
  loki:

services:
  loki:
    image: grafana/loki:2.0.0
    ports:
      - "3100:3100"
    command: -config.file=/etc/loki/local-config.yaml
    networks:
      - loki

  promtail:
    image: grafana/promtail:2.0.0
    volumes:
      - /var/log:/var/log
    command: -config.file=/etc/promtail/config.yml
    networks:
      - loki

  grafana:
    image: grafana/grafana:latest
    ports:
      - "3000:3000"
    networks:
      - loki

```

本機是linux 的話， /var/log/底下都會傳到 loki 日誌去
可以用 volume 把日誌掛到 containr 那邊去
應該也有一樣的效果
不過我下面還有比較好的方法


加 loki datasource 和 explore操作
[Loki轻量级日志系统_Kammingo的博客-CSDN博客](https://blog.csdn.net/Kammingo/article/details/115489954)

[輕量級日誌系統Loki原理簡介和使用_平凡人筆記 - MdEditor](https://www.mdeditor.tw/pl/pKL4/zh-tw)


* [使用 Loki 搭建个人日志平台 | 血衫非弧の一存](https://blog.kelu.org/tech/2020/01/31/grafana-loki-for-logging-aggregation.html)


## docker 實作 Loki 日誌

再 container 把日誌輸出來不是很方便
所以我們是使用 docker 直接輸出日誌
可算非常方便

參考來源: 
[loki收集docker容器里运行服务的日志,loki+grafana_十个菜-CSDN博客](https://blog.csdn.net/u014756339/article/details/111992382)

[docker swarm结合loki搭建轻量级日志监控系统 - 琴弦上的尼古丁](http://www.dlwxz.com/2020/07/docker-swarm%E7%BB%93%E5%90%88loki%E6%90%AD%E5%BB%BA%E8%BD%BB%E9%87%8F%E7%BA%A7%E6%97%A5%E5%BF%97%E7%9B%91%E6%8E%A7%E7%B3%BB%E7%BB%9F/)

### 安裝 docker loki driver

[Docker driver | Grafana Labs](https://grafana.com/docs/loki/latest/clients/docker-driver/)


```bash=
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions 
# 完成可用這個指令
docker plugin ls       
```

相關升級、移除可以在官網看到

其他
[Loki | 数据过期自动删除策略设计 - 华为云](https://www.huaweicloud.com/articles/f114e0a2a885a4ef8591dd11ff3590c7.html)

比較有趣看到yaml 模板寫法

```yaml=
version: "3.4"
 
x-logging:
  &loki-logging
  driver: loki
  options:
    loki-url: "http://YOUR_IP:3100/loki/api/v1/push"
    max-size: "50m"
    max-file: "10"
 
services:
  host:
    container_name: grafana
    image: grafana/grafana
    environment:
    - TZ=Asia/Shanghai
    - LANG=zh_CN.UTF-8
    logging: *loki-logging
# ————————————————
# 版权声明：本文为CSDN博主「2018_like菜」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
# 原文链接：https://blog.csdn.net/u014756339/article/details/111992382
```


## 樹梅派安裝法

參考:[docker plugin enable Loki gives an error · Issue #974 · grafana/loki · GitHub](https://github.com/grafana/loki/issues/974#issuecomment-766390258)

1. 嚐試安裝

```
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions 
```

2. 看到錯誤

```
Error response from daemon: dial unix /run/docker/plugins/61d4906b35d0d106db69561446160ba63bddb5f9a30d765293ddc75d11a6935b/loki.sock: connect: no such file or directory
```

3. git clone loki

```
git clone --depth=1 https://github.com/grafana/loki.git

```

4. 在 loki 資料夾輸入

```bash=

# 安裝 golang
sudo apt install golang
# 編譯
GOOS=linux GOARCH=arm GOARM=7 go build ./cmd/docker-driver
```

樹莓派 build 一直失敗，後來發想到 Golang 也是跨平台
所以我在x86主機編譯順利。

5. 編譯檔案(docker-driver)複製到/var/lib/docker/plugins/<ALPHA_NUMERIC_FOLDER>/rootfs/bin

把編譯好的 docker-driver，

```bash=
sudo ls /var/lib/docker/plugins/
# 有一個很長編碼資料夾就是

```

6. 啟動 docker plugin 

```bash=
docker plugin enable loki
```

這邊之前啟動的 docker containr 要刪掉重新起新的 container 才會有用


### 全局設定

編輯daemon.json。linux下默認路徑是/etc/docker/daemon.json (需要sudo)， windows則默認是%userprofile%\.docker\daemon.json


```json=
{
  "log-driver": "loki",
  "log-opts": {
    "loki-url": "http://YOUR_IP:3100/loki/api/v1/push",
    "max-size": "50m",
    "max-file": "10"
  },
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

參考:[loki收集docker容器裡運行服務的日誌,loki+grafana_十個菜-CSDN博客](https://blog.csdn.net/u014756339/article/details/111992382)


### 各自設定

在 docker-compose 裡面加 logging

```yaml=
version: "3.7"
services:
  logger:
    image: grafana/grafana
    logging:
      driver: loki
      options:
        loki-url: "http://localhost:3100/loki/api/v1/push"
```

就可以完成

指令可以用

```bash=
docker run --rm --name=grafana --log-driver=loki --log-opt loki-url="http://YOUR_IP:3100/loki/api/v1/push" --log-opt max-size=50m --log-opt max-file=10 grafana/grafana

# --log-driver=loki指定日志驱动器为loki
# --log-opt loki-url则指定了loki的url
# --log-opt max-size日志最大大小
# --log-opt max-file日志文件最大数量
```

參考:* [loki收集docker容器里运行服务的日志,loki+grafana_十个菜-CSDN博客](https://blog.csdn.net/u014756339/article/details/111992382)


## 使用 Loki 做 Traefik Acess Log 分析

loki 架好後，相關 docker log 設定完
Grafana 設定好 loki 資源
這邊記得把 traefik access log 改成 json

```yaml=
#traefik
    - "--accesslog.format=json"
```

import 這個 dashboard [Traefik Via Loki dashboard for Grafana | Grafana Labs](https://grafana.com/grafana/dashboards/13713)

還要把 json 內容裡面 label 改掉

```
{job=\"/var/log/traefik.log\"}
```

換成(這邊換成你電腦相對位置)

```
{container_name=\"rpi-traefik_traefik_1\",host=\"PI202\"}
```

[Traefik 2 监控系统之Grafana Prometheus Promtail Loki完美结合 - 简书](https://www.jianshu.com/p/68955d911fa4)
詳細也可以看這篇，也寫得很完整


也有看到非常強的 dashboard
[Loki v2 Web Analytics Dashboard for NGINX dashboard for Grafana | Grafana Labs](https://grafana.com/grafana/dashboards/12559)