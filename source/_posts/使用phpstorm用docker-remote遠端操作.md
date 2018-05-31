---
title: 使用phpstorm用docker remote遠端操作
date: 2018-05-26 16:08:38
tags: [docker, phpstorm]
categories: docker
---

最近在測試phpstorm
因為不想在window 10上灌docker
想遠端連docker
發現phpstorm好像有遠端控制選項

{% asset_img phpstorm_docker.png phpstorm設定docker選項 %}

<!--more-->

在使用內建遠端時候
起注意這個會有安全性的問題
至於我在家裡使用
不需要考慮到這一塊
暫時不考慮TSL那一塊

之前有買「Docker入門與實戰」
但是書上docker daemon用不出來

後來看了網路用法不太一樣
[远程连接docker daemon，Docker Remote API | Deepzz's Blog](https://deepzz.com/post/dockerd-and-docker-remote-api.html)

https://docs.docker.com/engine/reference/commandline/dockerd/#description
```sh
sudo <path to>/dockerd -H 0.0.0.0:5555 &
```
打上這樣久能開啟5555 port給phpstorm操作使用
不過在第一次開啟感覺還是打指令比較方便耶...


當然也可以照[远程连接docker daemon，Docker Remote API | Deepzz's Blog](https://deepzz.com/post/dockerd-and-docker-remote-api.html)
設定/etc/docker/daemon.json在執行`dockerd`

這樣就能愉悅在phpstorm玩docker

