---
title: 查看 IP 指令和路由筆記
date: 2021-07-14 20:45:37
tags: [network,ip ,route]
categories: 網路
---


之前寫[一張網卡使用多組 IP 方法 - 使用上設定防火牆陷阱 | 程式狂想筆記](https://malagege.github.io/blog/2020/05/30/%E4%B8%80%E5%BC%B5%E7%B6%B2%E5%8D%A1%E4%BD%BF%E7%94%A8%E5%A4%9A%E7%B5%84-IP-%E6%96%B9%E6%B3%95/#%E4%BD%BF%E7%94%A8%E4%B8%8A%E8%A8%AD%E5%AE%9A%E9%98%B2%E7%81%AB%E7%89%86%E9%99%B7%E9%98%B1)有談過`使用上設定防火牆陷阱`，彈要怎麼看出去方法`ip route show`，但我對輸出出來資訊不太懂，今天特別研究一下。


<!--more-->

## ip 詳細指令

[linux IP命令简介 - 簡書](https://www.jianshu.com/p/5f933834a5c2) [備份圖](https://i.imgur.com/LXEBoQA.png)

我找出介紹詳細指令簡介，建議可以看一下，相關問題可以解決。


## 所以要怎麼看出去 ip?


### ip route show 

```
#ip route show
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
default via 192.168.99.1 dev enp0s8 proto static metric 101
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
10.244.0.0/24 via 10.244.0.0 dev flannel.1 onlink
10.244.1.0/24 dev cni0 proto kernel scope link src 10.244.1.1
10.244.2.0/24 via 10.244.2.0 dev flannel.1 onlink
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1
192.168.99.0/24 dev enp0s8 proto kernel scope link src 192.168.99.111 metric 101

```

> default表示默認的route，即其他route規則沒有生效就默認使用該條規則。兩條default規則分別對應不同的device。
> dev表示device，即使用的interface是哪個
> proto表示該條規則的protocol identifier，有：
> 
>     redirect，該路由是由於ICMP redirect產生(install)
>     kernel，該路由是在內核進行自動配置的時候產生的
>     boot，該路由是在bootup的時候產生
>     static，該路由是由管理員增加的，為了覆蓋動態路由
>     ra，該路由由Router Discovery tool產生
> 
> scope，同上面ip addr show介紹的scope
> src，表示為優先選擇的源地址，當給目標地址發包的時候
> metric，表示到目的地的距離(the 'distance' to the target)，是一個數字，其單位可以是：Hop, Delay, Throughput, Reliability

參考來源: 
[linux IP命令简介 - 簡書](https://www.jianshu.com/p/5f933834a5c2)
[通俗理解IP路由 - SegmentFault 思否](https://segmentfault.com/a/1190000019363010)

其實我還是覺得這樣還是很不方便看，`route`指令可以看到那些外部 IP 會倒哪個 Gateway。後面也會講 route 指令也被廢棄了...

這邊也可以設定一個固定 IP 會指定至到哪一個 Gateway，設定結果如下。

```
10.88.22.11 via 172.26.15.254 dev eth1 
10.88.22.12 via 172.26.15.254 dev eth1 
10.88.22.13 via 172.26.15.254 dev eth1 
```
這邊我觀查 via 後面是放 Gateway IP，前面是目標 IP 會指定一個 Gateway 傳出去。程式寫太久，再回來看真的會忘記...

#### 小記看 ip route 技巧

```
default via 10.89.8.254 dev eth0 
10.88.22.11 via 172.26.15.254 dev eth1 
10.88.22.12 via 172.26.15.254 dev eth1 
10.88.22.13 via 172.26.15.254 dev eth1 
10.89.8.0/24 dev eth0  proto kernel  scope link  src 10.89.8.88 
169.254.0.0/16 dev eth2  scope link  metric 1004 
169.254.0.0/16 dev eth1  scope link  metric 1005 
169.254.0.0/16 dev eth0  scope link  metric 1006 
172.26.15.0/24 dev eth1  proto kernel  scope link  src 172.26.15.30 
172.26.25.0/24 dev eth2  proto kernel  scope link  src 172.26.25.100 

```

步驟 

1. 尋找目標 IP 對應上面 via 關鍵字搜尋會指定到哪一個 Gateway ，沒有的話就看 default Gateway
2. 尋找 Gatway IP 範圍，如:10.89.8.254 => 10.89.8.0/24
3. 找出那一行的 src 10.89.8.88 

> src，表示為優先選擇的源地址，當給目標地址發包的時候


### ip a 

我覺得 `ip route show ` 比較難第一時間看出來，在想有什麼關鍵字可以看結果。

[linux IP命令简介 - 簡書](https://www.jianshu.com/p/5f933834a5c2)裡面有提到`secondary`當為外發的網絡包選擇默認源地址時不使用該地址

> secondary，當為外發的網絡包選擇默認源地址時不使用該地址

這樣來看確實可以馬上看出來，不過 `ifconfig` 沒有看到相關資訊。

```
# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
....
6: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether xx:xx:xx:xx:xx brd ff:ff:ff:ff:ff:ff
    inet 10.89.8.88/24 brd 10.89.8.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet 10.89.8.103/24 brd 10.89.8.255 scope global secondary eth0:103
       valid_lft forever preferred_lft forever
    inet 10.89.8.104/24 brd 10.89.8.255 scope global secondary eth0:104
       valid_lft forever preferred_lft forever
    inet 10.89.8.105/24 brd 10.89.8.255 scope global secondary eth0:105
       valid_lft forever preferred_lft forever
    inet 10.89.8.106/24 brd 10.89.8.255 scope global secondary eth0:106
       valid_lft forever preferred_lft forever
    inet 10.89.8.107/24 brd 10.89.8.255 scope global secondary eth0:107
       valid_lft forever preferred_lft forever
    inet 10.89.8.108/24 brd 10.89.8.255 scope global secondary eth0:108
       valid_lft forever preferred_lft forever
    inet 10.89.8.109/24 brd 10.89.8.255 scope global secondary eth0:109
       valid_lft forever preferred_lft forever
    inet 10.89.8.110/24 brd 10.89.8.255 scope global secondary eth0:110
       valid_lft forever preferred_lft forever
    inet 10.89.8.113/24 brd 10.89.8.255 scope global secondary eth0:113
       valid_lft forever preferred_lft forever
    inet 10.89.8.114/24 brd 10.89.8.255 scope global secondary eth0:114
       valid_lft forever preferred_lft forever
    inet 10.89.8.115/24 brd 10.89.8.255 scope global secondary eth0:115
       valid_lft forever preferred_lft forever
    inet 10.89.8.116/24 brd 10.89.8.255 scope global secondary eth0:116
       valid_lft forever preferred_lft forever
    inet 10.89.8.117/24 brd 10.89.8.255 scope global secondary eth0:117
       valid_lft forever preferred_lft forever
    inet 10.89.8.118/24 brd 10.89.8.255 scope global secondary eth0:118
       valid_lft forever preferred_lft forever
    inet 10.89.8.165/24 brd 10.89.8.255 scope global secondary eth0:165
       valid_lft forever preferred_lft forever
    inet 10.89.8.193/24 brd 10.89.8.255 scope global secondary eth0:193
       valid_lft forever preferred_lft forever
    inet 10.89.8.91/24 brd 10.89.8.255 scope global secondary eth0:91
       valid_lft forever preferred_lft forever
    inet6 fe80::f002:2aff:feff:b865/64 scope link 


```


## 被廢棄掉的指令(彩蛋)

有空詳細記錄。

ifconfig -> ip a
traceroute -> tracepath
[linux - traceroute command: replacement or alternative - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/500796/traceroute-command-replacement-or-alternative)
route -> ip route
[Deprecated Linux networking commands and their replacements | Doug Vitale Tech Blog](https://dougvitale.wordpress.com/2011/12/21/deprecated-linux-networking-commands-and-their-replacements/)

### 查看 ip expose port

[[轉]Linux 查看程式用哪些 port 方法 | 程式狂想筆記](https://malagege.github.io/blog/2020/09/28/Linux-%E6%9F%A5%E7%9C%8B%E7%A8%8B%E5%BC%8F%E7%94%A8%E5%93%AA%E4%BA%9B-port-%E6%96%B9%E6%B3%95/)

```bash
netstat -an |grep LISTEN 
ss -an |grep LISTEN
```