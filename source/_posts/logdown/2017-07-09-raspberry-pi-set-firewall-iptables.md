---
layout: post
title: 'Raspberry PI 防火牆設定iptables'
date: 2017-07-09 04:28
comments: true
categories: Linux
tags: [Raspberry Pi,iptables,Linux]
---
我的動物機設定，因為公司要報告
我使用reveal-md來報告
需要連回家裡電腦設定
因為之前沒有做記錄
就閱在做一下嘍:)
<!--more-->
非中華電信網路封鎖

```iptables.sh
iptables -F  //清除所有的已訂定的規則；
iptables -P INPUT DROP  //進來的封包全擋
iptables -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
# 選項與參數：
# -m ：一些 iptables 的外掛模組，主要常見的有：
#      state ：狀態模組
#      mac   ：網路卡硬體位址 (hardware address)
# --state ：一些封包的狀態，主要有：
#      INVALID    ：無效的封包，例如資料破損的封包狀態
#      ESTABLISHED：已經連線成功的連線狀態；
#      NEW        ：想要新建立連線的封包狀態；
#      RELATED    ：這個最常用！表示這個封包是與我們主機發送出去的封包有關
iptables -A INPUT -s 192.168.1.0/24 -j ACCEPT //內網192.168.1.0網域可以進來
iptables -A INPUT -i lo -j ACCEPT //localhost可以連  -i interface

#iptables -A INPUT -p tcp –dport 80 -j ACCEPT  //全部可以連

#taiwan ip input http:80
iptables -A INPUT -s 61.216.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 218.160.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 210.62.248.0/21 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 211.20.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 211.72.128.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 211.72.0.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 210.242.0.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 210.71.128.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 203.75.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 202.39.128.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 103.25.236.0/22 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 1.160.0.0/12 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 114.32.0.0/12 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 122.120.0.0/13 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 60.249.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 60.250.0.0/15 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 59.124.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 220.128.0.0/18 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 114.30.32.0/20 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 61.217.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 61.228.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 211.22.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 211.21.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 211.75.0.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 210.242.128.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 210.61.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 203.74.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 203.66.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 43.255.92.0/22 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 1.34.0.0/15 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 114.24.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 118.160.0.0/13 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 122.116.0.0/15 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 125.224.0.0/13 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 60.248.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 175.111.192.0/18 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 61.218.0.0/15 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 61.220.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 211.23.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 59.112.0.0/13 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 211.75.128.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 210.241.224.0/19 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 210.59.128.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 210.65.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 203.69.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 36.224.0.0/12 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 111.240.0.0/12 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 118.168.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 122.118.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 125.232.0.0/15 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 168.95.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 59.120.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 218.168.0.0/13 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 61.224.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 61.70.239.0/24 -p tcp --dport 80 -j ACCEPT


#3 ip-prefix split
iptables -A INPUT -s 202.39.0.0/18 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 202.39.64.0/19 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 220.129.0.0/13 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 220.137.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 220.141.0.0/15 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 220.143.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 220.128.64.0/17 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 220.128.192.0/18 -p tcp --dport 80 -j ACCEPT


#emonme ip-prefix
iptables -A INPUT -s 211.79.32.0/20 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 221.120.0.0/18 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 221.120.64.0/19 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 116.59.0.0/16 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 114.136.0.0/15 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 111.70.0.0/15 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 111.80.0.0/14 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 223.136.0.0/13 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -s 42.64.0.0/12 -p tcp --dport 80 -j ACCEPT



#tranmission
iptables -A INPUT -s 192.168.1.0/24 -p tcp --dport 9091  -j ACCEPT
iptables -A INPUT -p tcp --dport 59999:61000 -j ACCEPT
```



[U型遊樂園--海底兩萬里: 防火牆-iptables的state(3)](http://crazyduke.blogspot.com/2009/11/iptablesstate.html)
[iptables 拒绝所有的连接，再指定 ip 连接? - V2EX](https://www.v2ex.com/t/235392)