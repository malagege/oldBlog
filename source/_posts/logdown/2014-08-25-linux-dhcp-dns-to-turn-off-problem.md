---
layout: post
title: 'Linux DHCP DNS重開機跑掉的問題'
date: 2014-08-25 17:54
comments: true
categories: Linux
tags: [Linux,DHCP,DNS]
---
在Google搜尋到解決方法[Linux DHCP DNS設定問題](http://ithelp.ithome.com.tw/question/10071258)
有空測試一下，先記錄一下。

>主機使用 DHCP 取得 IP，有時後會出現明明就設定好了，但是重開機後DNS的設定又跑掉的問題。
若是有此問題，請修改該網卡的設定。
設定完DNS後，請修改網卡的部份。(假設網卡為eth0)
請加入PEERDNS=no後重啟網路或重開機就行了。

>詳細內容請參考http://blog.faq-book.com/?p=2717