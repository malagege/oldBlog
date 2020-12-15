---
title: "[轉]Linux 查看程式用哪些 port 方法"
date: 2020-09-28 23:27:14
tags: [linux]
categories: Linux
---

[linux如何查看端口被哪个进程占用 - 与f - 博客园](https://www.cnblogs.com/fps2tao/p/10042553.html)
最近看到這篇實用
筆記

<!--more-->

本文介紹linux如何查看端口被哪個進程佔用的方法：

1、lsof -i:端口號


2、netstat -tunlp|grep 端口號

都可以查看指定端口被哪個進程佔用的情況

【步驟一】lsof -i
{% asset_img img1.png title %}

lsof -i 用以顯示符合條件的進程情況，lsof(list open files)是一個列出當前系統打開文件的工具。以root用戶來執行lsof -i命令，如下圖

【步驟二】lsof -i:端口號

{% asset_img img2.png title %}

lsof -i:端口號，用於查看某一端口的佔用情況，比如查看22號端口使用情況，lsof -i:22，如下圖

【步驟三】netstat -tunlp

netstat -tunlp用於顯示tcp，udp的端口和進程等相關情況，如下圖

{% asset_img img3.png title %}

【步驟四】netstat -tunlp|grep 端口號

{% asset_img img4.png title %}

netstat -tunlp|grep 端口號，用於查看指定端口號的進程情況，如查看22端口的情況，netstat -tunlp|grep 22，如下圖

 

 

轉: https://jingyan.baidu.com/article/546ae1853947b71149f28cb7.html