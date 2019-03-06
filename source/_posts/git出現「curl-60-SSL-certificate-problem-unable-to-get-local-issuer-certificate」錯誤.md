---
title: >-
  git出現「curl: (60) SSL certificate problem: unable to get local issuer
  certificate」錯誤
date: 2019-01-12 15:20:13
tags:
categories:
---

今天發生怪事
公司內網 gitlab 網址 ip 換成網域導致 socuretree 跟 git bash 不能用
後來再找解決的方法

<!--more-->

##解決方法

### git remote 改成 ssh 網址

走 ssh 方式就能順利解決 git 連線的問題

### 改 curl.cainfo(此方法跟這個沒關係)

```
    php.ini 中的 openssl.cafile 設定
    php.ini 中的 curl.cainfo 設定
    php.ini 中的 /usr/local/etc/openssl/cert.pem 設定 (Homebrew provided)
```

我發現用瀏覽器網頁都正常，但是在 curl 上面就不正常...
但我這個是 gitlab...，所以跟 php 沒有什麼關係
[labs.beyond.com.tw » Yosemite 上 Guzzle 遇到的 openssl certificate 問題](http://labs.beyond.com.tw/blog/?p=685)

### 憑證看起來沒辦法用在內網上面

瀏覽器網頁都正常，但是在 curl 上面就不正常...，我還不確定能不能這樣用
不過看到下面這篇
[SSL 证书是不是不能绑定 192.168.1.1 这类本地地址？ - 梅林 - KoolShare - 源于玩家 服务玩家](http://koolshare.cn/thread-64823-1-1.html)

其他參考來源：
[使用 plesk ssh chrooted 遇到 git clone https SSL 問題 | SSORC.tw](https://ssorc.tw/6440)
[蚊子館: Lighttpd - HTTPS(SSL)](http://linux-guys.blogspot.com/2010/12/lighttpd-httpsssl.html)
[用 curl 和 wget 略過 SSL 憑證 - 筆記 | Mr. 沙先生](https://shazi.info/%E7%94%A8-curl-%E5%92%8C-wget-%E7%95%A5%E9%81%8E-ssl-%E6%86%91%E8%AD%89-%E7%AD%86%E8%A8%98/)
[curl: (60) SSL certificate problem: unable to get local issuer certificate 错误 - 不忘初心，方得始终 - CSDN 博客](https://blog.csdn.net/sanbingyutuoniao123/article/details/71124655)
[sourcetree 的远程创库验证名字出错，但是记住名字。如何修改？ - 知乎](https://www.zhihu.com/question/28654385)
[git 如何避開 SSL 憑證問題 | 老洪的 IT 學習系統](https://snippetinfo.net/media/1973)
[通过 SourceTree 连接 SSL 有问题的自建 gitlab 服务器 - fishmai 的专栏 - CSDN 博客](https://blog.csdn.net/fishmai/article/details/76400931)
