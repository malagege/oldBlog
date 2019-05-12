---
title: "mysql備份遇到#mysql50#不能匯出的問題"
date: 2019-05-10 22:15:56
tags: [mysql]
categories: MySQL
---

今天專案做 mysqldump 時候，遇到`#mysql50#...`資料表不能備份
等一下，DB 裡面沒有這個資料表
後來我同事再網路找到了原因

<!--more-->

> 都报错，后来发现，mysql 用户的\$HOME 目录下有目录.ssh 导致的问题。

[mysql 数据库中出现#mysql50#.ssh 处理方法 - 花名:白起 - CSDN 博客](https://blog.csdn.net/levy_cui/article/details/51395995)

刪除.ssh 資料夾就能解決
這個雷也太... 囧
