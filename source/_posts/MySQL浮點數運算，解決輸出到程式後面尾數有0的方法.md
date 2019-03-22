---
title: MySQL浮點數運算，解決輸出到程式後面尾數有0的方法
date: 2019-03-13 22:46:25
tags: [sql, mysql, zero]
categories: SQL
---

最近用 MySQL 做浮點數運算
PHP 程式輸出竟然有`123.30000`
後來網路上有看到解法

<!--more-->

> 正確的解決方式：
> select 0+cast(90.09008700 as char ) 輸出結果 90.090087
> 或者
> select 0+CONVERT(‘90.09008700‘ , CHAR) 輸出結果：90.090087

參考內容:
[mysql 之處理金錢小數點後的多余 0 - IT 閱讀](https://www.itread01.com/content/1535301491.html)
[SQL 中如何去掉 decimal 字段後面的 0 - donnie88888888 的专栏 - CSDN 博客](https://blog.csdn.net/donnie88888888/article/details/52439299)
