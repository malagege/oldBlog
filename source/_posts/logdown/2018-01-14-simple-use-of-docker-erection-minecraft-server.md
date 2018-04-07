---
layout: post
title: '簡單使用docker架設Minecraft-server'
date: 2018-01-14 03:04
comments: true
categories: Docker
tags: [Docker]
---
最近想架一個Minecraft-Server
但我想找一個比較方便的方法裝
後來找到[itzg/minecraft-server - Docker Hub](https://hub.docker.com/r/itzg/minecraft-server/)

<!--more-->

簡單使用
```
docker run -d -v /path/on/host:/data \
    -e EULA=TRUE \
    -p 25565:25565 -e EULA=TRUE --name mc itzg/minecraft-server
 ```

Docker Hub分數還滿高的image

server.properties的設定可以用`-e xxx=xxx`參數使用

然後發現[dockerfiles/minecraft-server at master · itzg/dockerfiles](https://github.com/itzg/dockerfiles/tree/master/minecraft-server)作者有寫好docker-compose.yml檔

可直接使用`docker-compose up -d`
可以在`docker-compose.yml`檔設定一些environment variable
和沒有要使用的rcon移除

要關閉就使用`docker-compose down`
但這個不會移除container跟volume
`docker-copmpose down -f` 移除container
或著
`docker-compose down -v`移除container和volume
