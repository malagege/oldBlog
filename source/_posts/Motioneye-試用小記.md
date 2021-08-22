---
title: 使用 Docker 架設 Motioneye 試用小記
date: 2021-08-22 15:36:26
tags: [camera,motioneye,docker]
categories: Linux
---

原本想研究[MieszkoMakuch/pi-security-camera: University project: Smart security camera on raspberry pi with email notifications and web interface](https://github.com/MieszkoMakuch/pi-security-camera)，來當做監視器，但前置作業就要先搞好 OpenCV，最近看到 [HomeLab.001 樹梅派ARM架構升級版 ODROID N2 搭建多功能服務器](https://botiostudio.com/homelab-odroid-n2/) 這篇提到 Motioneye (影像監視) ，看介紹感覺都符合我要使用的。

<!--more-->

## 網路上教學

- [Raspberry Pi 筆記(68)：影像監視系統motioneye | 天花板隨記](https://atceiling.blogspot.com/2020/03/raspberry-pi-68motioneye.html)
- [用 Raspberry pi 做一個網路監控系統吧！ - 叩頂窩客](https://koding.work/use-raspberry-pi-motioneye-make-surveillance-system/)

好多人都有拿來用，目前我先用筆電小試。

## 簡單使用 Docker

官網就有 Docker 實作，我這邊先整理我在筆電執行的指令。

```bash=
docker run --name="motioneye" \
    --device=/dev/video0 \
    -p 8765:8765 \
    --hostname="motioneye" \
    -v /etc/localtime:/etc/localtime:ro \
    -v /etc/motioneye:/etc/motioneye \
    -v /var/lib/motioneye:/var/lib/motioneye \
    --restart="always" \
    --detach=true \
    ccrisan/motioneye:master-amd64
```

這樣就完成了。

這邊注意，`/dev/video0` 是指你的攝像頭。

去 `http://192.168.1.111:8765`進去介面，帳號/密碼為 admin/(空白)，進去新增Camera 就能完成。沒想到還滿快的。



## 失敗小記

我原先去 Docker Hub 找 [jshridha/motioneye - Docker Image | Docker Hub](https://hub.docker.com/r/jshridha/motioneye)，但執行不能成功。

但我發現，指令跟官方有差異。

```bash=
docker run -d --name=motioneye \
    --device=/dev/video0 \
    -p 8081:8081 \
    -p 8765:8765 \
    -e TIMEZONE="America/New_York" \
    -e PUID="99" \
    -e PGID="100" \
    -v /mnt/user/appdata/motioneye/media:/home/nobody/media \
    -v /mnt/user/appdata/motioneye/config:/config \
    jshridha/motioneye:latest
```

```
    -e PUID="99" \
    -e PGID="100" \
```

這個好像跟攝像頭權限有關係，但在我機器要使用

```
    -e PUID="0" \
    -e PGID="0" \
```

不知道為什麼官方就可以不用加???


## 未來研究

NOTE

1. Line / Disocrd 通知、Email 通知
2. 智米攝影機 改機(RTSP)
  - [小方智慧攝影機 改機支援RTSP (加映Surveillance Station設定) - Mobile01](https://www.mobile01.com/topicdetail.php?f=494&t=5246923)
  - [samtap/fang-hacks: Collection of modifications for the XiaoFang WiFi Camera](https://github.com/samtap/fang-hacks)
  - [Modify the Xiaomi Xiaofang Camera to Work With Home Assistant and Other Video Recording Software | Automated Home Party](https://automatedhome.party/2017/12/18/modify-the-xiaomi-xiaofang-camera-to-work-with-home-assistant-and-other-video-recording-software/)
  - [米家智慧攝影機1080P 改機支援RTSP (Surveillance Station設定) - Mobile01](https://www.mobile01.com/topicdetail.php?f=494&t=5390712)
  - [Release 0.8.7 RTSP working back (Tested) · Filipowicz251/mijia-1080P-hacks](https://github.com/Filipowicz251/mijia-1080P-hacks/releases/tag/0.8.7)
  - [telmomarques/xiaomi-360-1080p-hacks: Hacks for the Xiaomi Mi 360 1080p camera (MJSXJ02CM).](https://github.com/telmomarques/xiaomi-360-1080p-hacks)
  - http://bobbyromeo.com/home-automation/xiaomi-smart-1080p-wifi-ip-camera-rtsp-streaming-hack/#firmware


還在猶豫刷機還是買支援RSTP 的 IP Camera，刷機沒法刷回台版。有機會無法用米家APP操作。

當然也有考慮找支援RSTP 協定的攝影機。
