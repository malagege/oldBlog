---
title: SQL not in & in 拆開寫法
date: 2018-07-03 21:06:04
tags: [NOT IN,IN,SQL]
categories: SQL
---

今天思緒有點短路，做SQL用程式組兩個`NOT IN`
~~竟然用` OR `，可能覺得跟IN一樣~~
但事後想想應該是要用`AND`
不可能有一個要`NOT IN`另外一個不用`NOT IN`

好了這個雷紀錄一下，不要再踩到

* IN(xxx,ooo)  => IN (xxx) or  IN(ooo)=
* NOT IN(xxx,ooo)  => NOT IN (xxx) AND  NOT IN(ooo)