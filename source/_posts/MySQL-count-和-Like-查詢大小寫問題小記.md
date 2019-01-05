---
title: MySQL count 和 Like 查詢大小寫問題小記
date: 2018-12-19 20:42:04
tags: [mysql,like]
categories: MySQL
---

MySQL最近做`count`時候
發現有一個寫法很特別
特別記錄一下

另外MySQL `like`查詢預設不分大小寫
其他DB我記得預設會分大小寫
不常寫應該很容易忘記

<!--more-->

## DB  LIKE到底有沒有分大小寫呢?

MySQL , SQL Server沒有分
Oracle,DB2有分

> 要讓mysql查詢區分大小寫，可以：
    select * from table_name where binary a like 'a%'
[MySQL 的 LIKE 查詢區分大小寫 | InspireGate 派克空間](http://inspiregate.com/programming/mysql/363-mysql-like-queries-are-case-sensitive.html)

* [SQL：SELECT英數字不分大小寫、不分全半形、忽略空白 @ 姊姊❉心得報告 :: 痞客邦 ::](http://lanfar.pixnet.net/blog/post/40706817-sql%EF%BC%9Aselect%E8%8B%B1%E6%95%B8%E5%AD%97%E4%B8%8D%E5%88%86%E5%A4%A7%E5%B0%8F%E5%AF%AB%E3%80%81%E4%B8%8D%E5%88%86%E5%85%A8%E5%8D%8A%E5%BD%A2%E3%80%81%E5%BF%BD)

[sql - How do you force mysql LIKE to be case sensitive? - Stack Overflow](https://stackoverflow.com/questions/14007450/how-do-you-force-mysql-like-to-be-case-sensitive)

[Java程式教學甘仔店: Oracle SQL LIKE的用法教學](http://pclevin.blogspot.com/2014/05/oracle-sql-like.html)


## count 

今天問比較資深工程師會怎麼用
他說他會用`count(1)`
不過仔細查看了一下
`count(欄位)`是我平常使用的方法，我知道當`null`時候不會算進去
`count(1)`會算`null`值


```
执行效果上：  
count(*)包括了所有的列，相当于行数，在统计结果的时候，不会忽略列值为NULL  
count(1)包括了忽略所有列，用1代表代码行，在统计结果的时候，不会忽略列值为NULL  
count(列名)只包括列名那一列，在统计结果的时候，会忽略列值为空（这里的空不是只空字符串或者0，而是表示null）的计数，即某个字段值为NULL时，不统计。
--------------------- 
作者：BigoSprite 
来源：CSDN 
原文：https://blog.csdn.net/iFuMI/article/details/77920767 
版权声明：本文为博主原创文章，转载请附上博文链接！
```
[count(1)、count(*)与count(列名)的执行区别 - iFuMI的博客 - CSDN博客](https://blog.csdn.net/iFuMI/article/details/77920767)
{% asset_link web1.png 備份圖 %}
[Select Count(*) / Count(1) / Count(欄位名) 的差異 | Jeff 隨手記 - 點部落](https://dotblogs.com.tw/jeff-yeh/2011/01/12/20767)



最近太忙了，因為還沒有時間消化
所以先放在這邊

* [Fake2db - もっともらしいダミーデータを作る事ができるPythonツール | ソフトアンテナブログ](https://www.softantenna.com/wp/software/fake2db/)
* [bhrgu/fake2db-tests: Several tests for the fake2db project](https://github.com/bhrgu/fake2db-tests)
* [MySQL-如何刪除hash表分區 - 掃文資訊](https://hk.saowen.com/a/f3d3f46df39716db1ddd1a69d38c07a9e92b5793e5b745b6f5c0e64e07b9056c)
* [MYSQL的分区字段，必须包含在主键字段内 - 王 庆 - 博客园](https://www.cnblogs.com/zhishan/p/3285055.html)
* [mysql 的 partition与key的关系、限制 - barfoo的专栏 - CSDN博客](https://blog.csdn.net/barfoo/article/details/4242863)
* [mysql 表分區技術 - 掃文資訊](https://hk.saowen.com/a/8354a112014a1c52bfd80f3d81b1ae06d2b2d59b1bc1fd71f57f5e614f42580c)
* [［MySQL］MySQL分区与传统的分库分表 - 海天的笔记本 | haitian-coder](http://haitian299.github.io/2016/05/26/mysql-partitioning/)
* [MySQL Partitioning a VARCHAR(60) - Stack Overflow](https://stackoverflow.com/questions/47423873/mysql-partitioning-a-varchar60)
* [聊聊partition的方式 - code-craft - SegmentFault 思否](https://segmentfault.com/a/1190000011704687)
* [MySQL分区总结 - iVictor - 博客园](https://www.cnblogs.com/ivictor/p/5033708.html)
* [Orcale搜尋資料且不分大小寫 @ 這是林葛格的窩 :: 隨意窩 Xuite日誌](https://blog.xuite.net/superman23.lin/twblog/155325150-Orcale%E6%90%9C%E5%B0%8B%E8%B3%87%E6%96%99%E4%B8%94%E4%B8%8D%E5%88%86%E5%A4%A7%E5%B0%8F%E5%AF%AB)
* [SQL：SELECT英數字不分大小寫、不分全半形、忽略空白 @ 姊姊❉心得報告 :: 痞客邦 ::](http://lanfar.pixnet.net/blog/post/40706817-sql%EF%BC%9Aselect%E8%8B%B1%E6%95%B8%E5%AD%97%E4%B8%8D%E5%88%86%E5%A4%A7%E5%B0%8F%E5%AF%AB%E3%80%81%E4%B8%8D%E5%88%86%E5%85%A8%E5%8D%8A%E5%BD%A2%E3%80%81%E5%BF%BD)
* [Select Count(*) / Count(1) / Count(欄位名) 的差異 | Jeff 隨手記 - 點部落](https://dotblogs.com.tw/jeff-yeh/2011/01/12/20767)
* [count(1)、count(*)与count(列名)的执行区别 - iFuMI的博客 - CSDN博客](https://blog.csdn.net/iFuMI/article/details/77920767)
* [20萬筆資料，Mysql資料庫就跑不動囉! (排序相關)](https://www.puritys.me/docs-blog/article-17-20%E8%90%AC%E7%AD%86%E8%B3%87%E6%96%99%EF%BC%8CMysql%E8%B3%87%E6%96%99%E5%BA%AB%E5%B0%B1%E8%B7%91%E4%B8%8D%E5%8B%95%E5%9B%89!-(%E6%8E%92%E5%BA%8F%E7%9B%B8%E9%97%9C).html)
* [linux - How to do the port forwarding from one ip to another ip in same network? - Server Fault](https://serverfault.com/questions/586486/how-to-do-the-port-forwarding-from-one-ip-to-another-ip-in-same-network)
* [15個優化你的sql Query的方式 | Davidou的 Blog](http://blog.davidou.org/archives/609)
* [Undefined index php - Google 搜尋](https://www.google.com/search?client=firefox-b-ab&q=Undefined+index+php)
* [網站設計筆記: PHP Notice: undefined index 完美解决方法](http://alfredwebdesign.blogspot.com/2013/05/php-notice-undefined-index.html)
* [[解決] PHP Notice: Undefined index和Undefined variable的解决方法 « 關於網路那些事...](https://adon988.logdown.com/posts/4701973)
* [對 MySQL 的 VARCHAR 欄位使用 INDEX 時可以增加效率的方法… – Gea-Suan Lin's BLOG](https://blog.gslin.org/archives/2012/07/17/2911/%E5%B0%8D-mysql-%E7%9A%84-varchar-%E6%AC%84%E4%BD%8D%E4%BD%BF%E7%94%A8-index-%E6%99%82%E5%8F%AF%E4%BB%A5%E5%A2%9E%E5%8A%A0%E6%95%88%E7%8E%87%E7%9A%84%E6%96%B9%E6%B3%95/)
* [SQL：SELECT英數字不分大小寫、不分全半形、忽略空白 @ 姊姊❉心得報告 :: 痞客邦 ::](http://lanfar.pixnet.net/blog/post/40706817-sql%EF%BC%9Aselect%E8%8B%B1%E6%95%B8%E5%AD%97%E4%B8%8D%E5%88%86%E5%A4%A7%E5%B0%8F%E5%AF%AB%E3%80%81%E4%B8%8D%E5%88%86%E5%85%A8%E5%8D%8A%E5%BD%A2%E3%80%81%E5%BF%BD)
* [MySQL 的 LIKE 查詢區分大小寫 | InspireGate 派克空間](http://inspiregate.com/programming/mysql/363-mysql-like-queries-are-case-sensitive.html)
* [为什么oracle区分大小写？_oracle_帮酷编程问答](http://hant.ask.helplib.com/oracle/post_706515)
* [sql - How do you force mysql LIKE to be case sensitive? - Stack Overflow](https://stackoverflow.com/questions/14007450/how-do-you-force-mysql-like-to-be-case-sensitive)
* [SQL Fiddle](http://sqlfiddle.com/#!15/9314a/16)
* [Like Case Sensitive in MySQL - Stack Overflow](https://stackoverflow.com/questions/8083455/like-case-sensitive-in-mysql)
* [postgresql like case insensitive - Google 搜尋](https://www.google.com/search?client=firefox-b-ab&q=postgresql+like+case+insensitive&spell=1&sa=X&ved=0ahUKEwi8-LWd-qvfAhWKvbwKHWR6C-kQBQgpKAA&biw=1920&bih=966)
* [MySQL 裡搜尋 CHAR/VARCHAR (String) 欄位時要注意的事情 – Gea-Suan Lin's BLOG](https://blog.gslin.org/archives/2014/02/09/4237/mysql-%E8%A3%A1%E6%90%9C%E5%B0%8B-charvarchar-string-%E6%AC%84%E4%BD%8D%E6%99%82%E8%A6%81%E6%B3%A8%E6%84%8F%E7%9A%84%E4%BA%8B%E6%83%85/)