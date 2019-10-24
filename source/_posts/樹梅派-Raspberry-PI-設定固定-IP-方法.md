---
title: 樹梅派(Raspberry PI)設定固定 IP 方法
date: 2019-10-24 21:25:32
tags: [raspberry,pi]
categories: Linux
---

Raspberry PI 設定小記
因為最近中華 P883 偶爾連不到 Raspberry Pi
重開 P883 就能使用
不知道是不是在中華數據機設定就會很不穩
所以我想改成設定在 PI 上面

<!--more-->

## 設在 Router 機器 vs 設定主機 好處

一般有這兩種，各有優缺點

假如 Router 機器掛掉，裡面資料 IP 有備份可能不用重新設定
但需要買一樣的 數據機

相反主機也是這個道理，目前我看我做網路朋友是選`設定主機`

## 設定方法

/etc/dhcpcd.conf
```
interface eth0
static ip_address=192.168.1.177
static routers=192.168.1.1
static domain_name_servers=192.168.1.1
```

## 以前方法

還是紀錄一下

######################
/etc/network/interfaces
######################
iface eth0 inet static
address x.x.x.x
netmask x.x.x.x
network x.x.x.x
broadcast x.x.x.x
gateway x.x.x.x

######################
/etc/resolv.conf
######################
nameserver x.x.x.x

參考來源:
[葉難: Raspberry Pi：固定私有IP與dhcpcd](http://yehnan.blogspot.com/2016/05/raspberry-piipdhcpcd.html)
[小狐狸事務所: 設定樹莓派區網固定 IP](http://yhhuang1966.blogspot.com/2019/02/ip.html)
[將樹莓派 (Raspberry Pi) 的 eth0 / wlan0 網路介面設定固定 IP - 黃忠義 - Medium](https://medium.com/@chungyi410/%E5%B0%87-raspberry-pi-%E7%9A%84-eth0-%E7%B6%B2%E8%B7%AF%E4%BB%8B%E9%9D%A2%E8%A8%AD%E5%AE%9A%E5%9B%BA%E5%AE%9A-ip-43010aa3effb)