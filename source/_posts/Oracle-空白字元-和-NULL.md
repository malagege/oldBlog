---
title: Oracle 空白字元 和 NULL
date: 2020-09-08 23:16:40
tags: [oracle]
categories: Oracle
---

之前有聽說 Oracle 空白字元跟 NULL 會是一樣的
但沒搞清楚
今天果然踩到地雷了
趕快來釐清一下

<!--more-->

空白字元查 insert into 進去時候，Oracle 會轉成 NULL
所以查詢 select ... where xxx is **null** 要放 null
空白字元查詢會找不到

[Oracle中的NULL、’’（空字符串）以及’_’（空格） - Memory4Young - 博客园](https://www.cnblogs.com/memory4young/p/use-null-empty-space-in-oracle.html)