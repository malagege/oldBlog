---
title: Raspberry Pi 使用 Seafile 初體驗
date: 2020-10-12 22:41:02
tags: [seafile, nextcloud]
categories: Linux
---

之前使用[NextCloud 使用docker簡易安裝筆記 | 程式狂想筆記](https://malagege.github.io/blog/2018/09/16/NextCloud-%E4%BD%BF%E7%94%A8docker%E7%B0%A1%E6%98%93%E5%AE%89%E8%A3%9D%E7%AD%86%E8%A8%98/#more)
登入頁面就要等了老半天
進去聽歌點一首歌也要等一下
原本以為他上傳檔案有做加解密
但我後來發現 NextCloud PI 預設沒打開
把幾個套件拿掉也一樣慢

不過也又 NextCloud 一陣子
因為他竟然有支援 Chrome Media Hub 
可進行上一首、下一首
![](https://storage.googleapis.com/gweb-uniblog-publish-prod/original_images/Chrome_Media_Control.gif)
[Manage audio and video in Chrome with one click](https://blog.google/products/chrome/manage-audio-and-video-in-chrome/)
可算非常方便

但最近我覺得太慢了
原本以為是 CPU 的問題
後來看到官網說是 IO 速度太慢
所以我最近在找找有沒有替代方案

<!--more-->

## 相關其他搜尋



以下幾乎是資料夾內容同步，目前沒使用就先不研究

- [Sharry：一个非常好用的文件分享程序-荒岛](https://lala.im/7154.html)
- [ResilioSync：公私兼备的同步盘-荒岛](https://lala.im/4737.html)
- [使用Syncthing自建私有同步盘-荒岛](https://lala.im/4731.html)
- [Dropit：一个简洁的文件上传工具-荒岛](https://lala.im/6617.html)
- [CozyCloud：基于GO语言开发的网盘/同步盘-荒岛](https://lala.im/4698.html)
CozyCloud 是 Golang 寫的，沒有音樂播放功能，感覺跟 seafile 差不多
之後會再嘗試看看，主要看有沒有不加密檔案功能

## Seafile

### 優點、缺點

#### 優點

1. 速度比 NextCloud 快
2. 基本檔案上傳管理功能都有
3. 可以編輯 Markdown 檔案

我目前只想到這樣 = =a
但這個就夠讓人跳了

#### 缺點

1. NextCloud 很多功能都沒有 (DLNA,Media Hub)
2. 檔案上傳一定會做加密，不能手動關掉(聽說是為了做歷程關係)
3. 上傳大檔案可能 PI 會等很久(可能再加密部分) 我有測試 1GB 大概也要卡很久

### 音樂解決方案

[Clementine 音樂播放器](https://www.clementine-player.org/zh_TW/)

裝這個撥放器可以跟 Seafile 串接
目前介面還算能接受
音樂不會全部抓到硬碟
這個可能我還滿需要這樣的

我有看到他有支援 Subsonic
但是我使用覺得介面不是很好用用

大缺點就是需要安裝撥放器


## 輕鬆使用 docker 體驗 Seafile

### build images

```bash
# 請使用 Root 權限，官網是這樣說的。但我看裡面語法沒什麼問題
# 感覺怕權限失敗
git clone https://github.com/domenukk/seafile-docker-pi.git
cd ./seafile-docker-pi/image
make base && make server 
```

### run 

```bash
docker run -d --name seafile   \
-e SEAFILE_SERVER_HOSTNAME=192.168.1.202:8888  \
-p 8888:80 \
seafileltd/seafile:pi
```
因為我 80 已經有用 NextCloud 
執行會有點久，你看他的 log 看起來完成
其實還沒有跑完，等一回就好了

https 下次應該會考慮就 Caddy 去做反向代理
[Traefik 搭配 Docker 自動更新 Let’s Encrypt 憑證 | 小惡魔 - 電腦技術 - 工作筆記 - AppleBOY](https://blog.wu-boy.com/2019/01/traefik-docker-and-lets-encrypt/)
不過也有考慮使用 Traefik

預設帳號密碼是 `me@example.com` / `asecret`

相關教學[用Docker部署 Seafile 服务 - Seafile服务器手册中文版](https://manual-cn-origin.seafile.com/deploy/deploy_with_docker)


#### 自訂密碼

```
docker run -d --name seafile \
  -e SEAFILE_SERVER_HOSTNAME=seafile.example.com \
  -e SEAFILE_ADMIN_EMAIL=me@example.com \
  -e SEAFILE_ADMIN_PASSWORD=a_very_secret_password \
  -v /opt/seafile-data:/shared \
  -p 80:80 \
  seafileltd/seafile:pi
```

#### Let's encrypt SSL certificate

```bash
docker run -d --name seafile \
  -e SEAFILE_SERVER_LETSENCRYPT=true \
  -e SEAFILE_SERVER_HOSTNAME=seafile.example.com \
  -e SEAFILE_ADMIN_EMAIL=me@example.com \
  -e SEAFILE_ADMIN_PASSWORD=a_very_secret_password \
  -v /opt/seafile-data:/shared \
  -p 80:80 \
  -p 443:443 \
  seafileltd/seafile:pi
```

#### 修改設定重啟 Seafile

設定檔路徑 `shared/seafile/conf`

docker restart seafile

#### 加一個管理者

```
docker exec -it seafile /opt/seafile/seafile-server-latest/reset-admin.sh
```


#### 檔案路徑

/shared 為根路徑

-v /opt/seafile-data:/shared 

```bash
docker run -d --name seafile   \
-e SEAFILE_SERVER_HOSTNAME=192.168.1.202:8888  \
-p 8888:80 \
-v /media/USBdrive/seafile-data:/shared \
seafileltd/seafile:pi
```

#### 垃圾回收

docker exec seafile /scripts/gc.sh.


### 其他相關文章

- [树莓派部署 seafile 和 nextcloud 比较 - 知乎](https://zhuanlan.zhihu.com/p/140289336)
- [domenukk/seafile-docker-pi: A Docker image for Seafile server - Raspberry Pi Edition](https://github.com/domenukk/seafile-docker-pi)
- [开源的私有云网盘程序Seafile安装教程-荒岛](https://lala.im/671.html)