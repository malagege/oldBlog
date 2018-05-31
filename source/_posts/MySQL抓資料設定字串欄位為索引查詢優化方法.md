---
title: MySQL抓資料設定字串欄位為索引查詢優化方法
date: 2018-05-26 00:40:01
tags: [SQL,MySQL, index, MySQL優化]
categories: MySQL
---

常把搜尋條件用在索引上面
可以加快查詢
今天實驗一個東西，把uid加入到index上面
但是`explain`竟然還是full scan
index還會有掃全表的問題
覺得很神奇
就稍微研究一下
筆記筆記

<!--more-->

{% asset_img slow.png 一般查詢 %}

沒有用`*`查詢，竟然還會full scan
Google查詢一下，varchar做為index好像不太好(不確定是這個問題，相關資訊太少)

就換了一下查法

{% asset_img fast.png "primary key,index key查詢" %}

竟然就正常...

那試試*看看好了...
{% asset_img normal.png "primary key,index key查詢" %}
full scan

最後試試，IN優化方法(使用inner join )

{% asset_img best.png "primary key,index key查詢" %}

**注意where用哪張表，select就要用另一張表**

這樣就不會full scan了
愉悅!!!!

參考來源：
* [对mysql中last_insert_id()的新理解-博客即日起停止更新-51CTO博客](http://blog.51cto.com/sucre/723808)
* [[数据库与SQL] - No.5 MYSQL实现 RANK函数排序功能 - CSDN博客](https://blog.csdn.net/tjuyanming/article/details/77825875)
* [Mysql row number()排序函数的用法和注意_Mysql_脚本之家](http://www.jb51.net/article/87777.htm)
* [MySQL rownum的實作與排名的方法 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10136053)
* [mysql的最佳索引攻略 · Mysql设计与优化专题 · 看云](https://www.kancloud.cn/thinkphp/mysql-design-optimalize/39319)
* [MySQL慢查询Explain Plan分析 - CSDN博客](https://blog.csdn.net/leyangjun/article/details/52846534)
* [【mysql的设计与优化专题(6)】mysql索引攻略 - 菜问 - 博客园](http://www.cnblogs.com/nixi8/p/4574709.html)
* [MySQL数据库中EXPLAIN的使用及其注意事项详解 - 51CTO.COM](http://database.51cto.com/art/201108/285341.htm)
* [MYSQL EXPLAIN语句的extended 选项学习体会 - cy163 - 博客园](http://www.cnblogs.com/cy163/archive/2009/05/28/1491473.html)