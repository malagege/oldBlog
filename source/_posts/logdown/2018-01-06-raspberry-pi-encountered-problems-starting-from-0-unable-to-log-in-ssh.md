---
layout: post
title: 'Raspberry pi 從0開始遇到問題(無法登入ssh)  & 更新Jessie升級'
date: 2018-01-06 11:43
comments: true
categories: Linux
tags: [Raspberry Pi]
---
最近好想把Apache升級到2.4，這樣我就能使用http2
不過因為樹莓派一直卡在Wheezy
原本想說原升怎麼沒到2.4
後來發現Jessie才能升...
想說怎麼都沒有更新
現在版本是Stretch

<!--more-->

好想升Apache 2.4阿
![](https://i.imgur.com/88ccSit.gif)

最先有試著用[葉難: Raspberry Pi：如何更新Raspbian](http://yehnan.blogspot.com/2014/09/raspberry-piraspbian.html)
`rpi-upgrade`更新上去，但是沒有更新最高

[Raspbian 從 7 Wheezy 升級到 8 Jessie - 亂碼中的煉金術](http://blog.dreambreakerx.com/2015/10/upgrade-raspbian-from-wheezy-to-jessie/)
>    修改檔案 /etc/apt/sources.list 把第一行所有的 “wheezy” 置換為 “jessie” ，修改後大概是這樣 “deb http://mirrordirector.raspbian.org/raspbian/ jessie main contrib non-free rpi”
 修改檔案 /etc/apt/sources.list.d/raspi.list 把第一行所有的 “wheezy” 置換為 “jessie”，第一行最後加上 “ ui” ，修改後大概是這樣 “deb http://archive.raspberrypi.org/debian jessie main ui”
    建立資料夾 /home/pi/.config/autostart ， “mkdir /home/pi/.config/autostart” (注意 config 前面有 “.” )
    更新軟體套件庫 “sudo apt-get update”
    升級系統及軟體 “sudo apt-get -y dist-upgrade” (這過程大概兩小時， “-y” 是不詢問直接安裝)

改socues.list可以試試`sudo sed -i 's/wheezy/jessie/g' /etc/apt/sources.list`
改完執行下面，就更新完成
```sh
sudo apt-get update
sudo apt-get -y dist-upgrade
```

更新前最好備份SD會比較好
我有apache2跟vsftpd有壞掉
下面幾篇會補一下我遇到的問題

更新期間還會問要不要保留設定檔
通常我都會選D ，看有異動什麼檔案
假如沒有問題，就直接更換

中間我原本想wheezy直接升到Stretch
所以直接把wheezy改成stretch
但好像升級有問題，可能不執直接跳兩階XD
網路上有些人說用`rpi-upgrade`，有些說不要用`rpi-rpgrade`
但後面都有用`apt-get update  & apt-get - y dist-upgrade`



## 最新燒入新版img預設沒有開啟ssh

>開啟SSH功能
2016-11-25:
* SSH disabled by default; can be enabled by creating a file with name "ssh" in boot partition
意思應該是說創建一個名叫"ssh"的文件在boot分區,應該就是內存卡的根目錄。

在/boot目錄  `touch ssh`
開機才會啟動ssh
重新燒img想說怎麼連不到XD
原來新版有改

#swap 更新完吃2G
`/etc/dphys-swapfile`設定`CONF_SWAPSIZE=100`
存檔之後`service dphys-swapfile start`就正常了
https://blog.gtwang.org/iot/raspberry-pi/raspberry-pi-swap-configuration-using-usb-stick/




## 參考來源
[raspbian 系统配置中遇到的坑 NOOBS_v2_4_0_MozartC_新浪博客](http://blog.sina.com.cn/s/blog_6c9c71840102woam.html)
[Raspberry Pi 的基礎 - 沒螢幕、沒鍵盤、沒網路，居然也能設定 Wi-FI 連線 | IT 技術家](http://blog.itist.tw/2017/03/headless-wifi-setup-without-monitor-keyboard-or-cables-for-raspberry-pi-zero-w-and-3-model-b.html)
[Enable HTTP2 in Apache on Ubuntu 16.04 | DigitalOcean](https://www.digitalocean.com/community/questions/enable-http2-in-apache-on-ubuntu-16-04)
[\[網站\] 網域搬新家、升級 HTTPS 與 HTTP/2（長篇大論版） – Digiologist](https://lcarbon.idv.tw/%E7%B6%B2%E7%AB%99-%E7%B6%B2%E5%9F%9F%E6%90%AC%E6%96%B0%E5%AE%B6%E3%80%81%E5%8D%87%E7%B4%9A-https-%E8%88%87-http2%EF%BC%88%E9%95%B7%E7%AF%87%E5%A4%A7%E8%AB%96%E7%89%88%EF%BC%89/)
[Raspberry Pi 的基礎 - 沒螢幕、沒鍵盤、沒網路，居然也能設定 Wi-FI 連線 | IT 技術家](http://blog.itist.tw/2017/03/headless-wifi-setup-without-monitor-keyboard-or-cables-for-raspberry-pi-zero-w-and-3-model-b.html)
[树莓派系统升级Raspbian Wheezy到Raspbian Jessie(树莓派2B+） – 默默的点滴](http://www.mobibrw.com/2017/9851?utm_source=tuicool&utm_medium=referral)