---
title: 'MYSQL 時間運算 筆記'
date: 2018-05-31 21:19:48
tags: [MySQL, UNIX_TIMESTAMP, to_days,]
categories: MySQL
---

平常用到時間運算，假如能在SQL撈到想要的資料
這樣程式可以寫得少，速度也跟者快...
但是偏偏MySQL時間運算不太熟
在此網路找一些，寫筆記....

<!--more-->

[MySQL :: MySQL 5.5 Reference Manual :: 12.7 Date and Time Functions](https://dev.mysql.com/doc/refman/5.5/en/date-and-time-functions.html)

`UNIX_TIMESTAMP()`可以用between...and做比較
* [mysql - UNIX_TIMESTAMP() comparison - Stack Overflow](https://stackoverflow.com/questions/20642626/unix-timestamp-comparison)

### UNIX_TIMESTAMP()<==>FROM_UNIXTIME()

`UNIX_TIMESTAMP()`產生UNIX時間戳記
`FROM_UNIXTIME()`轉一般時間標記(xxxx-xx-xx oo:oo:oo)

### DATEDIFF('2015-09-28 21:00:01','2015-08-31 13:00:01');
算出差幾天
帶進去參數不一定要DATETIME格式
UNIX_TIMESTAMP也可以

### TIMEDIFF('2015-09-28 21:00:01','2015-09-28 13:23:11')
算出差幾秒，傳回時間格式(datetime)
[php - MySQL: how to get the difference between two timestamps in seconds - Stack Overflow](https://stackoverflow.com/questions/3528219/mysql-how-to-get-the-difference-between-two-timestamps-in-seconds/3528228#3528228)

**不同於一般DB2和Oracle資料庫用法**

[MySQL Tryit Editor v1.0](https://www.w3schools.com/sql/trymysql.asp?filename=trysql_func_mysql_timediff2)
可用MySQL函式,day(),month()....來做判斷幾天或幾個月

* [使用MySQL時間函數(function)轉換UNIX時間戳記與時間加減 @ 史丹利愛碎念 :: 痞客邦 ::](http://newaurora.pixnet.net/blog/post/204336313-%E4%BD%BF%E7%94%A8mysql%E6%99%82%E9%96%93%E5%87%BD%E6%95%B8%28function%29%E8%BD%89%E6%8F%9Bunix%E6%99%82%E9%96%93%E6%88%B3%E8%A8%98%E8%88%87)

### TIMESTAMPDIFF(DAY,'2015-09-28 21:00:01','2015-09-28 13:23:11')

[php - mysql timediff to hours - Stack Overflow](https://stackoverflow.com/questions/2174058/mysql-timediff-to-hours)

### to_days<=>from_days
to_days()傳回天數數字
+1就是加1天

看範例比較快
[糯米玩PC: MySql 按日期進行統計](http://sanujpblue.blogspot.tw/2014/12/mysql.html)
[TO_DAYS日期函数 - 我的地盘 - ITeye博客](http://282120928-qq-com.iteye.com/blog/1289492)
```
SELECT from_days(to_days(now())+1);   
```
[MySQL Tryit Editor v1.0](https://www.w3schools.com/sql/trymysql.asp?filename=trysql_func_mysql_from_days)
直接試比較快懂，我爬了很久
以為是UNIX_TIMESTAMP....**但不是**

### time_to_sec<=>sec_to_time

用時間格式轉乘秒，可以超過24小時
(感覺能用在UNIX_TIMESTAMP運算上面)

[MySQL TIME_TO_SEC() Function](https://www.w3schools.com/sql/func_mysql_time_to_sec.asp)



* [MySQL日期時間函數大全 @ 資訊園 :: 痞客邦 ::](http://fecbob.pixnet.net/blog/post/39089591-mysql%E6%97%A5%E6%9C%9F%E6%99%82%E9%96%93%E5%87%BD%E6%95%B8%E5%A4%A7%E5%85%A8)
* [sql - Compare dates in MySQL - Stack Overflow](https://stackoverflow.com/questions/3651985/compare-dates-in-mysql)
* [MYSQL时间函数 - 简书](https://www.jianshu.com/p/59dfdb9918cd)
* [mysql - SELECT row by DATEPART() - Stack Overflow](https://stackoverflow.com/questions/13402012/select-row-by-datepart)

### MySQL跟DB2 差異寫法

回憶以前用db2的時候

[项目中用到过的MYSQ和DB2的部分函数转化 （zxz）_MySQL_第七城市](http://www.th7.cn/db/mysql/201712/260897.shtml)
https://hackmd.io/iQcvSsh_Td6T6oxDjwWXew?view
備份用...
[MySQL :: MySQL 5.5 Reference Manual :: 12.7 Date and Time Functions](https://dev.mysql.com/doc/refman/5.5/en/date-and-time-functions.html#function_quarter)

### UNIX_TIMESTAMP 時間加法運算

select now() +  INTERVAL 5 HOUR

`now()`跟`CURRENT_TIMESTAMP`是一樣的

很常看到都是用DATETIME型態做運算

datetime運算其實有`DATE_ADD`和`DATE_SUB`
> DATE_ADD和DATE_SUB其實可以通用，因為expr可以為一個負數。
其實很簡單，一個是用加一個是減，不過...DATE_ADD(xxx, - INTERVAL 5 MINUNT) 可以用負數

最後，感覺能用在TIME_TO_SEC用在運算上面
但不知道網路上很少這樣用



### 小心效能
# MySQL裡的NOW()與UNIX_TIMESTAMP()

date(欄位) = xxx 很吃效能
盡量用 WHERE `date` >= '2010-10-01' AND `date` < '2010-11-01'

* [date - Difference between UNIX_TIMESTAMP and NOW() in MySQL - Stack Overflow](https://stackoverflow.com/questions/14849911/difference-between-unix-timestamp-and-now-in-mysql)
* [mysql中日期比较大小的方法 - CSDN博客](https://blog.csdn.net/qq2712193/article/details/48766575)
* [MySQL compare DATE string with string from DATETIME field - Stack Overflow](https://stackoverflow.com/questions/2758486/mysql-compare-date-string-with-string-from-datetime-field/17169859#17169859)
* [MYSQL数据库时间字段INT,TIMESTAMP,DATETIME性能效率比较](http://www.piaoyi.org/database/MYSQL-INT-TIMESTAMP-DATETIME.html)
* [datetime - How to optimize date time search in Mysql? - Stack Overflow](https://stackoverflow.com/questions/4233095/how-to-optimize-date-time-search-in-mysql)
* [MySQL中的UNIX_TIMESTAMP函数使用总结 - 曾是土木人 - 博客园](http://www.cnblogs.com/hongfei/archive/2012/06/08/2541599.html)



where DATE(`time_stamp`) = DATE(now());

用在欄位很吃效能，可能會造成full scan
datetime要怎麼比較今天呢??
這個我相信很常用
有找到這個
`WHERE startTime >= '2010-04-29' AND startTime < ('2010-04-29' + INTERVAL 1 DAY)`
[MySQL compare DATE string with string from DATETIME field - Stack Overflow](https://stackoverflow.com/questions/2758486/mysql-compare-date-string-with-string-from-datetime-field/17169859#17169859)
可以參考上面第二個解答
對非欄位做轉換比較不會吃資源
雖然不知道為什麼最佳解答是第一個吃效能的
呵呵


相信可能還有更好用的
可能我沒有記錄到
不過我相信我應該會很常回來看這篇


參考來源:
* [mysql - selecting rows in the last 5 minutes using unix time stamp - Stack Overflow](https://stackoverflow.com/questions/13064516/selecting-rows-in-the-last-5-minutes-using-unix-time-stamp)
* [MySQL :: MySQL 8.0 Reference Manual :: 12.7 Date and Time Functions](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_addtime)
* [sql - Difference between now() and current_timestamp - Database Administrators Stack Exchange](https://dba.stackexchange.com/questions/63548/difference-between-now-and-current-timestamp)
* [database - Difference between SYSDATE(),NOW(),CURRENT_TIMESTAMP,CURRENT_TIMESTAMP() in MySQL? - Stack Overflow](https://stackoverflow.com/questions/28315254/difference-between-sysdate-now-current-timestamp-current-timestamp-in-mysq)
* [MySQL系统时间函数NOW(),CURRENT_TIMESTAMP(),SYSDATE()的区别 - Qiurf - 博客园](http://www.cnblogs.com/drcoding/p/4624851.html)