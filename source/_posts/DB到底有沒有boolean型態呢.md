---
title: DB到底有沒有boolean型態呢
date: 2018-11-09 22:16:00
tags: [sql,mysql,db2]
categories: SQL
---

最近做`SELECT TRUE` 在SQL的時候
發現adminer跑出來結果是`1`
覺得有點不科學

<!--more-->

在很久的時候，MySQL操作phpmyadmin設定欄位都會看到有boolean這個選項
就以為boolean有這個東西
由於過去大學專題java都是用rs.getBoolean(xxx)也沒有發現這個問題

上一間公司是使用DB2都用varchar(1) [Y,N]
所以沒有遇過這個問題

今天遇到這個狀況有點錯愕orz
覺得怪怪的

最後網路查了一下，MySQL實做上會把boolean當tinyint(1)使用


DB2剛好開docker玩
下sql發現不能新增boolean欄位型態!!!
Google一下發現db2沒有boolean型態
sql server,db2 做`select True from xxxxx(依照db不能)`
會產生錯誤

這時候我就很好奇oracle,sql server到底有沒有這個東西(boolean)

這邊簡單記錄
mysql,postgres 有mysql型態
oracle,sql server, db2都不能用boolean

SQL92建義用bit，但是oracle推使用number

目前我同事是使用tinyint(1)，不過他也不知道sql server沒有boolean型態....

可參考以下內容
* [sql - Boolean vs tinyint(1) for boolean values in MySQL - Stack Overflow](https://stackoverflow.com/questions/3751853/boolean-vs-tinyint1-for-boolean-values-in-mysql)

```
其中， linq to sql 自然就將 IsRetired 欄位當成 boolean 值，0 是 false, 1 是 true. 這在 sql server 是相當自然而沒有疑慮的事情。 
那在 Oracle 呢？Oracle 是資料庫的老大，當然不需要 follow 標準。(所以別怪 MS Office 不照 OpenDocument format (ODF) 的標準，而要自行搞一套 Office Open Xml 的標準) 
Oracle 不支援 bit 的 datatype，甚至在 Oracle vs. SQL Server中也指明了，bit 的相等的oracle 表達法是 number(1, 0)。 
是否我們需要存 boolean 值時，在oracle 就宣告成 number(1, 0) 呢？ 
將來如果出了一個 linq to oracle 的 privider，是否看到 number(1, 0) 就自行轉成 boolean 呢？ 看了很多前人的程式，都是自行定義的，如 char(1)，然後寫入值為 '0' 或 '1' ，也有寫成 'Y' 或 'N"，當然也有 'y' 或 'n'。 
實在令人頭痛。 
如果是您，不知道您會如何宣告呢？
```
突然想到我上一間公司是使用db2
那時候他們說只用`varchar`型態
boolean幾乎都是用`Y`,`N`
那時候也不知道為什麼...
現在終於知道了
只不過這樣註解是不是都要特別記?

* [请问db2有boolean数据类型吗？？-CSDN论坛](https://bbs.csdn.net/topics/30054994)
* [DB2中类似于ORACLE中的DUAL表的表 - Menjoy's 技术文摘 - ITeye博客](http://menjoy.iteye.com/blog/111737)
* [程湘之間: Oracle 學習(3) boolean 的資料欄位](http://charlesbc.blogspot.com/2008/08/oracle-3-boolean.html)
* [关于oracle的boolean问题 - mrdu_somefun的博客 - CSDN博客](https://blog.csdn.net/H12KJGJ/article/details/52785108)


然後我翻我買的MySQL裡面也沒有提到Boolean這型態!!!!
看來錯誤觀念要修正...