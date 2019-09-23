---
title: mysql date和date_format小記
date: 2018-12-24 22:33:58
tags: [mysql]
categories: MySQL
---

平常我都是用 date()
但今天看到程式使用 date_format()
好奇這兩個差異

<!--more-->

其實下面兩個都可以顯示時間
參數第一個不管用 date,datetime 都不會有錯誤問題
[sql - How to optimize mysql group by with DATE_FORMAT - Stack Overflow](https://stackoverflow.com/questions/50939499/how-to-optimize-mysql-group-by-with-date-format)
效能上其實兩者沒太大差別(如上連結)

```sql
select date_format( '2018-01-32 00:00:00' + INTERVAL 1 DAY,'%Y%-%m-%d');
```

```sql
select date( '2018-01-32 00:00:00');
```

之後有發現甚麼在來記錄

參考連結

- [MySQL Date Format: What Datatype Should You Use? We Compare Datetime, Timestamp and INT.](https://www.vertabelo.com/blog/technical-articles/what-datatype-should-you-use-to-represent-time-in-mysql-we-compare-datetime-timestamp-and-int)
- [Re: [SQL ] mysql date column index 問題 - 看板 Database - 批踢踢實業坊](https://www.ptt.cc/bbs/Database/M.1379428749.A.7E8.html)
- [mysql date comparison with date_format - Stack Overflow](https://stackoverflow.com/questions/13507642/mysql-date-comparison-with-date-format)
- [MySQL :: MySQL 8.0 Reference Manual :: 12.7 Date and Time Functions](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-functions.html#function_date)
- [MySQL :: MySQL 5.5 Reference Manual :: 9.1.3 Date and Time Literals](https://dev.mysql.com/doc/refman/5.5/en/date-and-time-literals.html)
- [mysql 日期与 date*format 的比较\_date*帮酷编程问答](http://hant.ask.helplib.com/mysql/post_2073079)
- [(MySQL) | Online editor and compiler](https://paiza.io/projects/e0Vw8A5Q4qB1Wa9HZtvzjA?language=mysql)
- [mysql 时间字段性能比较 - u011820505的博客 - CSDN博客](https://blog.csdn.net/u011820505/article/details/79756652)