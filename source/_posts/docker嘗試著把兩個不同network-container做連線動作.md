---
title: docker嘗試著把兩個不同network container做連線動作
date: 2019-01-06 20:23:07
tags: [docker]
categories: Docker
---

之前讀到 load balance 都沒有看到如何做 DB 處理
不可能各區域都有一個 DB 吧
不過之前有跟人聊過
在部屬程式的時候，做 load balance 在每一個分流 IP 設定可以設一樣東西
不過內部 DB 要怎麼共用呢? 不過聽說可以設定讓他們導向一樣的地方
講那麼多有點離題，因為這篇也不是做 load balance

docker 在不同專案 說不定也會用到共用 DB
今天是自己實驗小記
可能不會記錄很詳細
畢竟 docker network 我還是很不熟

<!--more-->

今天找了相關 docker network 東西
但還不是很了解，有找了幾篇文章

- [Docker 学习与实践 - 网络篇 - Waterstrong](https://blog.waterstrong.me/docker-networking/)
- [Docker 進階實務班](https://www.slideshare.net/philipzh/docker-78924025)
- [放棄思考: Docker Network 及 Docker Compose 教學](https://javatoybox.blogspot.com/2018/10/docker-network-docker-compose.html)

首先，我實作兩個 network 出來
想辦法讓他們的 container 可以相連

```
docker network create test_1
docker network create test_2
docker run --name some-mysql1 -e MYSQL_ROOT_PASSWORD=root -d --network=test_1 mysql --default-authentication-plugin=mysql_native_password
docker run --name some-mysql2 -e MYSQL_ROOT_PASSWORD=root -d --network=test_2 mysql --default-authentication-plugin=mysql_native_password
docker run --name adminer --network=test_1 -d -p  8080:8080 adminer
docker network connect test_2 adminer
```

主要最後重點
docker network connect `network_name` `container_name`
可以讓你的 container 連線

奇怪，docker run --network 只能塞一個 network_name
好像只透過 docker connect 才能做連結
不過 docker-compose 好像能設定兩個 network
感覺 docker network 也是一個大坑洞
之後有時間研究在筆記一下

之前聽到 k8s 可以做到 docker 監控，順便做重啟動作
剛好最近看到這個影片有介紹
[COSS 103 kubernetes 介紹 教學 指令操作 - YouTube](https://www.youtube.com/watch?v=wnKyJKqKiVE)
感覺還滿好用的東西
有空來研究看看
不過我覺得 docker 先搞熟再說

## MySQL 8.0 的雷

想說嘗試著用 docker 跑 mysql
adminer 一直登不進去
後來仔細看了一下 docker hub 的 pwd 有多`--default-authentication-plugin=mysql_native_password`
WTF...

> 因為 之前 MySQL 預設儲存密碼的方式是 mysql_native_password, 但 8.0 之後 預設的儲存方式為 caching_sha2_password, 說是又快又安全啦, 但讓人措手不及, 一堆 SQL 工具 (Navicat) 可能還來不及更新支援 就...

ORZ...

[MySQL8 Authentication plugin Change « Sam 的程式筆記](http://samchu.logdown.com/posts/7425945-mysql8-authentication-plugin-change)
[PHP: Requirements - Manual](http://php.net/manual/en/mysqli.requirements.php)
