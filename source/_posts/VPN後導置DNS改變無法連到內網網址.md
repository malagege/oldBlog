---
title: VPN後導置DNS改變無法連到內網網址
date: 2019-03-14 21:01:24
tags:
categories:
---

最近連到 VPN，有同事反應連不到內網的 gitlab
由於我們 MIS 有改 Gitlab 網址(原本是 IP)
改成網址(git.xxx.com.tw)
但是連 VPN 後怎麼連不到

<!--more-->

由於我有一個朋友是做網路的
所以有問他這個問題
當你連 VPN 的時候，可能對方會重新寫入你的路由設定
DNS 也會被改變(本次的地雷)

Linux 看路由方法

```bash
> ip route show
default dev ppp0 scope link
default via 192.168.1.1 dev wlp3s0 proto dhcp metric 600
168.95.xxx.xxx dev ppp0 proto kernel scope link src 118.x.xx.xxx
169.x.0.0/16 dev wlp3s0 scope link metric 1000
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1
192.168.1.0/24 dev wlp3s0 proto kernel scope link src 192.168.1.121 metric 600
```

window 看路由方法

```bash
> route print
IPv4 路由表
===========================================================================
使用中的路由:
網路目的地                 網路遮罩         閘道          介面       計量
          0.0.0.0          0.0.0.0      192.168.1.1    192.168.1.111     35
          0.0.0.0          0.0.0.0          6.0.0.1          6.1.1.1   5025
          6.0.0.0        255.0.0.0            在連結上           6.1.1.1    281
          6.1.1.1  255.255.255.255            在連結上           6.1.1.1    281
    6.255.255.255  255.255.255.255            在連結上           6.1.1.1    281
        10.10.1.0    255.255.255.0            在連結上         10.10.1.1    291
```

連 VPN 我看了一下結果，VPN 並非把所有 IP 都做 defualt gateway
他很明確把`10.10.xx.xx`那些 IP 導向那邊去

所以我在 VPN 就連`192.168.x.x`(gitlab IP 位置)
空白網頁(網址有導向 git.xxxx.com.tw)
這時候我懷疑了 DNS

DNS 看了一下
果然連 VPN 的時候跟沒連的 DNS 位置不一樣

然後過幾天，副理出國遠端電腦發現不能連 git
也是一樣的問題 XDD

寫一篇筆記，留意這個問題

網路小筆記
一般設定 GateWay IP，通常設定固定 IP 的時候，可能會不知道這是幹什麼用？
但是今天問同學，他有簡單說明這個用途
之前想說沒輸入這個應該可有可無也可以連

發送 IP 的時候，我們電腦在不同網域的時候
已定要送到 gateway
不然不同網域誰知道要往哪裡送出

所以我以前電腦就算沒有設定 gateway 連相同網段也不會有問題
相同網段可能不需要找吧(XD 不確定)

簡單有問同學，直接連別台 PC 電腦，那是不是也可以做一樣的事情
我同學說可以。恍然大悟，原來 gateway 設定對網路這一快也是很重要
今天又學到一個重要的知識!!!

nslookup 查詢 dns 解析方法
`server dns`
{% asset_img 1.jpg DNS查詢 %}
[PChome Online - 股市](http://faq.extra.pchome.com.tw/faq_content02.htm?exh_no=P000007&pexh_no=P000181&cexh_no=H000185&faq_id=327&qnum=1)
