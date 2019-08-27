---
title: SQL 做時間判斷條件需要注意的事情
date: 2019-08-27 20:55:03
tags: [sql]
categories: MySQL
---

好幾次採到 SQL 時間的地雷
今天來好好筆記

<!--more-->

## datetime between '2019-01-01' and '2019-02-01'

這個時候需要注意!!
由於 between 沒有放入時間資料
查詢結果會是 `2019-01-01 00:00:00` ~ `2019-02-01 00:00:00`
所以需要使用 `2019-01-01 00:00:00` ~ `2019-01-31 23:59:59`


## MySQL '0000-00-00' = 0 會是 true

之前讀到 MySQL '0000-00-00' 可等於 null
但是 '0000-00-00' = '' 會是 false
'0000-00-00' = 0 為 true
這邊就是`隱式轉換`
可看下面標題

## 隱式轉換 

'123_ff' = 1  為true

[MySQL 隐式类型转换 - ZZZ's blog](http://zzzsdust.com/articles/23/)
[价值百万的 MySQL 的隐式类型转换 - 互联网技术和架构](https://blog.devopszen.com/mysql_params)
[MySQL隐式转化整理 - Rollen Holt - 博客园](https://www.cnblogs.com/rollenholt/p/5442825.html)

其他想到在記錄!!

## 其他小記

[Oracle时间类型数据为0的bug(0000-0-0) - 北国风光的专栏 - CSDN博客](https://blog.csdn.net/li19236/article/details/51701374)