---
title: Pulse Secure 不能使用，重新安裝方法
date: 2019-10-24 21:32:46
tags: [vpn,pulse,secure,vpn]
categories: Windows
---

最近用 VPN 連到公司網域
但不明原因遠端只要關掉就打不開
只有在重新開機開起 Pulse Secure 可以使用
重開應用程式就不能了
然後過了好幾天，我登入更新完後，就不能用了
移除也移除不了...
網路上看也沒有人解決 orz

只好自己來!!!

<!--more-->

1.  移除應用程式，如果順利就重新安裝

不能安裝就繼續往下做，我是遇到 他說 **PulseSecureService** 不能stopped
我又看了一下服務，不能做設定。所以要想辦法移除
但偏偏不能移除orz

2.  sc.exe delete PulseSecureService，執行完需要重開機(PS: 我用powershell)

3. 安裝[NTU SSL VPN](https://ccnet.ntu.edu.tw/vpn/for-windows.html)VPN程式

不知道為什麼我用的VPN網頁裝下來不能用
但是安裝這個就能順利執行

希望看到的大家有解決問題!!


