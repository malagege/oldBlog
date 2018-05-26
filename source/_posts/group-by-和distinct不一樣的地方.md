---
title: group by 和distinct不一樣的地方
date: 2018-05-17 20:25:30
tags: [MySQL, distinct]
categories: SQL
---

今天做專案，使用到select xxx,ooo ,zzzz from xxxx group by xxx,ooo 語句(沒有搭配統計)
發現跟我想像中怪怪的
平常使用都是用
一開始我以為會有重複資料
但結果沒有
<!--more-->

不過發現執行結果跟distanct一樣事情
但我印象中db2不能這樣跑
db2一定要group by xxx,ooo,zzz才能跑
就算mysql做group by xxx,ooo跑出來的zzz也只有一個欄位
跑出來的zzz只會有一筆(假如有兩筆以上，不知道是不是照自然排序抓出來的)

然後找不到db2語法
但還是有找到，其他DBMS都不能跑
> 7) mysql的group by 語句可以select 沒有被分組的欄位，如 
select id,name,age from A group by age 這樣 
但是在orcale和sqlserver中是會報錯的。這個取出的id,name所在的行是每個分組中的第一行數據。
[www.zendei.com | 502: Bad gateway](http://www.zendei.com/article/9047.html)
[你搞懂 ORACLE、 SQLSERVER、MYSQL與DB2的區別了嗎_ZenDei技術網路在線](http://webcache.googleusercontent.com/search?q=cache:x3Lgo0kwHvgJ:www.zendei.com/article/9047.html&num=1&lr=lang_zh-TW&client=firefox-b-ab&hl=zh-TW&gl=tw&tbs=lr:lang_1zh-TW&strip=1&vwsrc=0)


* [Hive group by distinct性能调优 | LittleQ](https://sjq597.github.io/2016/04/24/Hive-group-by-distinct%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98/)
* [GROUP BY Clause: How Well Do You Know It?](https://academy.vertabelo.com/blog/group-by-clause-how-well-do-you-know-it-2/)
* [Syun: [SQL]GROUP BY 與 DISTINCT 的差異](http://syunguo.blogspot.tw/2013/04/sqlgroup-by-distinct.html)
* [Mysql group by 详解(写的比较详细)-贵子-51CTO博客](http://blog.51cto.com/ud1121/190562)
* [DB2数据库DISTINCT与GROUP BY 对比 - CSDN博客](https://blog.csdn.net/zhwe357/article/details/18324097)
* [MySQL的四种GROUP BY用法-博客-云栖社区-阿里云](https://yq.aliyun.com/articles/530343)
* [Comparison of database tools - Wikipedia](https://en.wikipedia.org/wiki/Comparison_of_database_tools)
