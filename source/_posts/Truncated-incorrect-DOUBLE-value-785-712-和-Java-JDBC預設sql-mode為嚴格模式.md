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

