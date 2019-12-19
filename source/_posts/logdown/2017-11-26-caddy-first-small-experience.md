---
layout: post
title: 'Caddy Web Server初次小體驗'
date: 2017-11-26 07:29
comments: true
categories: Linux
tags: [caddy,web server]
---
這是簡單caddy架設筆記
在FB上看到有人推這個
會自動建立 Let's Encrypt 憑證
不需要設定
就很好奇玩了一下

<!--more-->

在沒有設定Caddyfile
port預設為2015
支援日文、簡體中文、韓文檔案下載
哈，不知道能不能當hfs


在同目錄新增`Caddyfile`
官網文件[The Caddyfile Syntax](https://caddyserver.com/docs/caddyfile#lexical-syntax)
設定感覺不難



內容如下
```
localhost:80
root .
gzip
log
browse
```

browse可幾乎同等於autoindex，可惜沒有預設設定排序
不過會記憶選擇後的排序


可以指定`Caddyfile`目錄
`caddy -conf C:\Users\xxx\Desktop\caddy\Caddyfile`

短時間不知道能不能取代我的apache
不過目前懶的做更換

還發現有caddy-git這個東西
改天做研究