---
title: 'Truncated incorrect DOUBLE value: ''785+712'' 和 Java JDBC預設sql_mode為嚴格模式'
date: 2018-09-10 22:29:49
tags: [sql]
categories: SQL
---


最近遇到一些MySQL地雷，要好好好整理一下
正常DB已經設定非嚴格模式
今天遇到Java執行遇到Warning不會過
SQL執行如insert xxxx .....  select xxx ....
語法也沒有錯
phpmyadmin可以執行
java執行就是過不了
```
2018-09-05 09:45:10,428 ERROR {Thread-0} [xxxxx.rbt.client.lib.Function] : insertMusicBoxReportLog com.mysql.jdbc.MysqlDataTruncation: Data truncation: Truncated incorrect DOUBLE value: '785+712'
```
這就非常讓我疑惑

<!--more-->

首先，先研究這個錯誤問題
https://com-it.tech/archives/170855
才過一天...，網站就掛掉了 {% asset_link webbackup.png "google cache備份" %}
剛剛注意到下面有原網址[小心MySQL的隐式类型转换陷阱 | Sean's Notes](http://seanlook.com/2016/05/05/mysql-type-conversion/)


```
select '1aa1'=='1'
```
就會出現這個問題
好像自動轉型會發生狀況
產生**Warning**


JDBC不管怎麼樣，預設會自動開啟嚴格模式
> spring.datasource.url=jdbc:mysql://localhost:3306/conweb?sessionVariables=sql_mode='NO_ENGINE_SUBSTITUTION'&jdbcCompliantTruncation=false
爬到stackoverflow找到這篇[java - com.mysql.jdbc.MysqlDataTruncation: Data truncation: Data too long for column 'column_name' - Stack Overflow](https://stackoverflow.com/questions/13567155/com-mysql-jdbc-mysqldatatruncation-data-truncation-data-too-long-for-column-c)

看到可以設定sql_mode就有不好的預感

>根据“三、重要的SQL_MODE值”，因而可以获知，使用JDBC Driver作为Mysql Client，碰到"inserting an incorrect value into a column"的情况的时候，会抛出ERROR，而不是WARNING
JDBC Driver默认会设置会话SQL_MODE='STRICT_TRANS_TABLES'的原因是："enforce JDBC compliance on truncation checks"需要开启"STRICT_TRANS_TABLES"这个SQL_MODE，而在JDBC URL中存在着"jdbcCompliantTruncation"这个参数，该参数可以控制是否开启"enforce JDBC compliance on truncation checks"功能，当我们通过JDBC URL设定"jdbcCompliantTruncation=false"之后，也就不会去默认设置SQL_MODE='STRICT_TRANS_TABLES'了
最後找到這個確認[Mysql的SQL_MODE - CSDN博客](https://blog.csdn.net/dslztx/article/details/47176549)


{% asset_img sql.jpg 錯誤訊息 %}
原本不想動測試DB資料，後來還是手動把`785+712`資料拿掉就正常執行了


**2019-11-25**

建立 table 時後 column 欄位建議要 `NULL`
最近踩了一個雷，因為公司內部有兩個DB 做同步，一個用嚴格模式、一個不是
導致另一個嚴格模式資料沒有進來

sql_mode 用 Dbeaver 查詢結果跟 phpmyadmin 不一樣???
後來發現跟上次跟 Java 一樣採到一樣的雷
有些 client 會自動幫你設定 sql_mode
要使用 `SHOW GLOBAL VARIABLES LIKE 'sql_mode';` 會比較準

```sql
SELECT @@sql_mode;
-- STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION    for dbeaver
-- NO_ENGINE_SUBSTITUTION  for phpmyadmin

SHOW GLOBAL VARIABLES LIKE 'sql_mode';
-- NO_ENGINE_SUBSTITUTION

```

非嚴警模式下，Level Warning 層級不會跳錯誤
在非嚴警模式下可以 `SHOW WARNINGS` 看到資料
[MySQL :: MySQL 5.7 Reference Manual :: 5.1.10 Server SQL Modes](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html#sql-mode-strict)
```
Strict SQL Mode
Strict mode controls how MySQL handles invalid or missing values in data-change statements such as INSERT or UPDATE. A value can be invalid for several reasons. For example, it might have the wrong data type for the column, or it might be out of range. A value is missing when a new row to be inserted does not contain a value for a non-NULL column that has no explicit DEFAULT clause in its definition. (For a NULL column, NULL is inserted if the value is missing.) Strict mode also affects DDL statements such as CREATE TABLE. 
```

裡面都寫得很清楚
[MySQL :: MySQL 5.7 Reference Manual :: 13.1.18 CREATE TABLE Statement](https://dev.mysql.com/doc/refman/5.7/en/create-table.html)
[MySQL :: MySQL 5.7 Reference Manual :: 13.2.5 INSERT Statement](https://dev.mysql.com/doc/refman/5.7/en/insert.html)
可以搜尋 strict 關鍵字找
改table 欄位大寫也跟 sql_mode有關係

[MySQL alter table modify column failing at rows with null values - Stack Overflow](https://stackoverflow.com/questions/7828440/mysql-alter-table-modify-column-failing-at-rows-with-null-values)
[MySQL刪除重複資料 @ Nightmare的胡言亂語 :: 痞客邦 ::](https://nsysumis94.pixnet.net/blog/post/22022962)

參考來源
[MySQL 中数据类型的默认值 - default 约束 - MySQL 拾遗 - 简单教程，简单编程](https://www.twle.cn/c/yufei/mysqlfav/mysqlfav-basic-data-type-default.html)
[MySQL sql_mode 说明（及处理一起 sql_mode 引发的问题） - Sean's Notes - SegmentFault 思否](https://segmentfault.com/a/1190000005936172)
[被 MySQL sql_mode 深深伤害（ 上 ） - MySQL 拾遗 - 简单教程，简单编程](https://www.twle.cn/c/yufei/mysqlfav/mysqlfav-basic-sql_mode.html)
[被 MySQL sql_mode 深深伤害（ 中 ） - MySQL 拾遗 - 简单教程，简单编程](https://www.twle.cn/c/yufei/mysqlfav/mysqlfav-basic-sql_mode2.html)
[被 MySQL sql_mode 深深伤害（ 下 ） - MySQL 拾遗 - 简单教程，简单编程](https://www.twle.cn/c/yufei/mysqlfav/mysqlfav-basic-sql_mode3.html)

彩蛋
[Data View and Format · dbeaver/dbeaver Wiki · GitHub](https://github.com/dbeaver/dbeaver/wiki/Data-View-and-Format)
[Data migration · dbeaver/dbeaver Wiki · GitHub](https://github.com/dbeaver/dbeaver/wiki/Data-migration)