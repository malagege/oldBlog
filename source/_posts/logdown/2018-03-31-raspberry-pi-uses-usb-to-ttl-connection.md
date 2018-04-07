---
layout: post
title: 'Raspberry pi 使用USB To TTL連接'
date: 2018-03-31 05:16
comments: true
categories: Linux
tags: [Raspberry PI,TTL]
---
最近樹梅派網路越來越不穩定...
有時候對突然連不到...
重開主機就可以了
但這樣下去 不是辦法
所以買一個USB To TTL這個東西跟Raspberry PI 連接

<!--more-->

首先，[USB To TTL | Raspberry Pi台灣樹莓派](https://www.raspberrypi.com.tw/tag/usb-to-ttl/)
基本上照上面做就可以連接到了

不過...，不知到插上去的位置
[RPi Low-level peripherals - eLinux.org](https://elinux.org/Rpi_Low-level_peripherals#General_Purpose_Input.2FOutput_.28GPIO.29)
照上面對照GPIO就不會插錯了

也有發現用網路線連接方法[1.6 直接 連接到 另外一部電腦(用網路線) - 學習樹莓派--Raspberry Pi](https://sites.google.com/site/raspberypishare0918/home/di-yi-ci-qi-dong/zhi-jie-lian-bi-dian)
不過也懶的插來插去
買個TTL預防萬一

結果網路問題`ifdown eth0`再`ifup eth0` 就能work了....
不知道是DHCP問題還是樹莓派問題...
還要先繼續觀查...
