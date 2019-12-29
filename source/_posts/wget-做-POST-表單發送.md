---
title: wget 做 POST 表單發送
date: 2019-12-29 19:22:13
tags: [linux]
categories: Linux
---

最近收到一個工單
是有關於做大量資料做修改
目前有一隻程式做單一資料置換
要怎麼快速對大量資料直接用現有程式做呢?
那就多批發送表單吧

<!--more-->

Google 到 [使用curl和wget发送post请求_cws1214的专栏-CSDN博客](https://blog.csdn.net/cws1214/article/details/21440691) 可以簡單用 wget 使用
為什麼使用 wget 做呢? 這樣就可以留下 log 紀錄檔
相同的url 後面可以加?__idnum__
EX:`wget --post-data="user=user1&pass=pass1&submit=Login"  http://domain.com/path/page_need_login.php?12345`


接下來目錄下會有`page_need_login.php?12345`
這就可以當 log 資訊

快速解決!!