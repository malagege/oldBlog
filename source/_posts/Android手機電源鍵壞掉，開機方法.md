---
title: Android手機電源鍵壞掉，開機方法
date: 2018-05-19 01:32:38
tags: [Android]
categories: Android
---

最近紅米NOTE3電源鍵壞了很久，但是平常可以指紋辨識解鎖
有幾次不小心關機，但是強按還是有辦法開機
但今天不行了....，剛好買新機
想備份LINE，但是無法開機
我Google尋了很多方法
最終採取fastboot開機法

<!--more-->

[小米手机电源键坏了怎么办？怎么开机啊？_安卓手机_手机学院_脚本之家](http://www.jb51.net/shouji/193501.html)
這一篇使用結果沒用

FastBoot 紅米NOTE3 進入方法 `音量键-  + 电源键 3秒`
但是...，電源鍵壞掉...無法使用
不過可以插電源線充電插入時，按下`音量鍵-`
可以進入fastboot模式

最後，[[Linux] fastboot 用法學習歸納 - 代碼家](http://www.dayexie.com/detail1009393.html)
```ssh
sudo add-apt-repository ppa:nilarimogard/webupd8
### 我沒有執行上面，發現內鍵可以安裝
sudo apt-get update
sudo apt-get install android-tools-adb android-tools-fastboot
```
安裝FastBoot進入root，`fastboot reboot`
你的手機就會重新開機

{% asset_img cli.png title %}

愉悅~~~

手機設定畫面，第二章圖可以設定自動撥放
{% asset_img c1.png 操作1 %}

{% asset_img c2.png 操作2 %}

https://developers.google.com/web/updates/2017/09/autoplay-policy-changes
https://times.hinet.net/news/21737359


[[Linux] fastboot 用法學習歸納 - 代碼家](http://www.dayexie.com/detail1009393.html)
[adb & fastboot 常用指令 – HTC Flash Zone](https://htcfz.wordpress.com/2016/07/05/adb-fastboot-common-commands/)
