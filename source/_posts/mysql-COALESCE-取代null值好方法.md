---
title: mysql COALESCE 取代null值好方法
date: 2018-07-19 21:15:18
tags: [sql,mysql,value,coalesce]
categories: MySQL
---

之前前公司有用db2 value，現在用MySQL，找不太到類似與法
不過在IBM文件看到[IBM Knowledge Center - VALUE](https://www.ibm.com/support/knowledgecenter/en/SSEPEK_11.0.0/sqlref/src/tpc/db2z_bif_value.html)這語法類似COALESCE
這麼實用的東西，當然不能忘記XD
<!--more-->

value(xxx,ooo)
假如xxx是null就帶入後面的值

COALESCE(xxx,ooo,zzz,fff,...)
同上面一樣方法，xxx為null的話，判斷下一個(ooo)是不是為null，ooo為null再繼續判斷下去

db2 value(xx,ooo)  & COALESCE

參考來源:
[德瑞克：SQL Server 學習筆記: SQL Server：認識 COALESCE() 函數](http://sharedderrick.blogspot.com/2012/06/t-sql-coalesce.html)
[IBM Knowledge Center - VALUE](https://www.ibm.com/support/knowledgecenter/en/SSEPEK_11.0.0/sqlref/src/tpc/db2z_bif_value.html)
