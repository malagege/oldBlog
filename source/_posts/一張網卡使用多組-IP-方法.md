---
title: 一張網卡使用多組 IP 方法
date: 2020-05-30 01:56:29
tags: [ip]
categories: 網路
---

故事是這樣的，最近我們公司有多個專案，聽到都是放在獨立機器，各自一組IP。
最近我不小心 FTP 設定錯 IP，但是上傳檔案上去。發現 http server(IP對的)竟然更新了。
我覺得很奇怪不是不同台電腦，後來發現那一台設定很多虛擬 IP。
~~讓我們研究公司是如何省錢的~~

其實這個用途看到用一台電腦需要跨網段使用
不需要切換 IP
但不知道數量是不是有限制
用台多會不會有缺點?

<!--more-->

這名詞叫 IP Alias 
[鳥哥的 Linux 私房菜 -- 架設 Router](http://linux.vbird.org/linux_server/0230router.php#routing_ip_alias)

## window 

很簡單，去控制台設定，自動 dhcp 要先取消掉

{% asset_img window_ip.png 設定多組 IP %}


## linux

### 簡單設定

```bash
sudo ifconfig wlp3s0:0 192.168.0.1              

ifconfig wlp3s:0 down

# 重新匯入網路
sudo /etc/init.d/networking restart 
# 這邊我是用下面這個才成功
service network-manager restart          
```
[鳥哥的 Linux 私房菜 -- Linux 常用網路指令介紹](http://linux.vbird.org/linux_server/0140networkcommand.php)

查看狀態`ip -4 a`

```
3: wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    inet 192.168.1.117/24 brd 192.168.1.255 scope global dynamic noprefixroute wlp3s0
       valid_lft 76153sec preferred_lft 76153sec
    inet 192.168.1.150/24 brd 192.168.1.255 scope global secondary wlp3s0:0
       valid_lft forever preferred_lft forever
```

### 永久加入設定

$ /etc/network/interfaces 檔案加入下列內容

```
auto lo
iface lo inet loopback
iface enp0s25 inet static
address 192.168.1.168
netmask 255.255.255.0
gateway 192.168.1.254

### 新增第二筆網卡設定

auto enp0s25:0 inet static   
address 192.168.1.169
netmask 255.255.255.0
gateway 192.168.1.254
```
參考:[[Ubuntu] 單網卡綁多IP - Frank Lin - Medium](https://medium.com/@frank.yylin/ubuntu-%E5%96%AE%E7%B6%B2%E5%8D%A1%E7%B6%81%E5%A4%9Aip-d69dc755efb0)

然後透過 bind port 各自綁多台 IP
這樣就能一台當多台用了


### 其他 OS 設定方法

[CentOS Linux 靜態 IP 位址網路設定教學 - G. T. Wang](https://blog.gtwang.org/linux/centos-linux-static-network-configuration-tutorial/)
[Ubuntu 18.04 透過 netplan 設定網路卡 IP - Soul & Shell Blog](https://blog.toright.com/posts/6293/ubuntu-18-04-%E9%80%8F%E9%81%8E-netplan-%E8%A8%AD%E5%AE%9A%E7%B6%B2%E8%B7%AF%E5%8D%A1-ip.html)

