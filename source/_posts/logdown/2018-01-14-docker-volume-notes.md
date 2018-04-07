---
layout: post
title: 'docker volume筆記'
date: 2018-01-14 06:42
comments: true
categories: Docker
tags: [Docker,volume]
---
上一篇，記錄使用Docker架設Minecraft-server
突中還發現Volume要怎麼使用，以前沒有用對方法
在些記錄一下

<!--more-->

# docker查詢掛載資訊

一般使用volumes
```
docker run -d -P -v /usr/local/apache2/htdocs  httpd
```

```
docker inspect -f '{{.Mounts}}' container_id
```
查詢volumes目錄路徑
之前我以為覆製檔案只能用`docker cp`
現在不用煩腦怎麼覆製

但我還有一個遺問
怎麼知道container workdir路徑是什麼


# Docker image WorkDir 目錄

常常不知道，image 預設啟動目錄在哪裡，又要跑去翻文件
有時候想直接看到...，這時候下這個指令

```
docker image inspect httpd
```
可以看到WorkingDir路徑


# Docker 掛載常見寫法差別

```
docker run -P -d -v /usr/local/apache2/htdocs httpd
```
和
```
docker run -P -d -v /usr/local/apache2/htdocs:/usr/local/apache2/htdocs httpd
```
這個一樣

今天發現原來是不一樣的東西，
原本爬文找有沒有第一次volumes時候把image東西copy出來
`docker run -P -d -v /usr/local/apache2/htdocs httpd` 這個指令就是做這個動作
只不過volume存在`docker inspect musing_hopper | grep volume`
可以看到存在位置，所以`/usr/local/apache2/htdocs`是指container path


# Docker volumes 路徑

docker volumes 存在位置在`/var/lib/docker/volumes`需要有root權限才能進去

docker rm 並不會刪除volumes檔
想一次刪掉container跟volume嗎?
只要下這個指令`docker rm -v container_id`
就能玩成目地了

# Docker 快速刪除

又看到更外速刪除container,volume方法
```
docker container prune
or
docker rm $(docker ps -aq)
```

這個刪除volume也會把link清掉
```
docker rm -lv CONTAINER
```
更多內容在[docker容器删除了，link怎么还在？ - SegmentFault](https://segmentfault.com/q/1010000010367238)裡面



先記錄到這邊，感覺link也需要好好筆記
我竟然不知道要清除link這個東西，以為他會自動消失(汗)


題外話：
[Docker volume 簡單用法 | 只放拖鞋的鞋櫃](http://julianchu.net/2016/04/19-docker.html)
[10 Docker Tips and Tricks That Will Make You Sing A Whale Song of Joy | I care, I share, I'm Nathan LeClaire.](https://nathanleclaire.com/blog/2014/07/12/10-docker-tips-and-tricks-that-will-make-you-sing-a-whale-song-of-joy/)
[Remove dangling docker images](https://gist.github.com/anildigital/862675ec1b7bccabc311)
[Maxkit: Dockerfile 初步閱讀與學習紀錄](http://blog.maxkit.com.tw/2017/02/dockerfile.html)
[深入理解docker的link机制-博客-云栖社区-阿里云](https://yq.aliyun.com/articles/55912)
[清理Docker的container，image与volume · 零壹軒·笔记](http://note.qidong.name/2017/06/26/docker-clean/)
[docker容器删除了，link怎么还在？ - SegmentFault](https://segmentfault.com/q/1010000010367238)