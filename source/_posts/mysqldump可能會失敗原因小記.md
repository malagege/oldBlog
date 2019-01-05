---
title: mysqldump可能會失敗原因小記
date: 2019-01-05 20:30:13
tags: [mysql, mysqldump]
categories: MySQL
---

最近出現有 mysqldump 備份失敗問題
但也不知道是什麼原因導至失敗
不過這邊蒐集一些資訊

<!--more-->

## 蒐集相關資料

蒐集一些資訊

- [mysqldump：常用操作、案例分享、意外终止的原因以及解决方法 - 记录创业路上的一些想法 - CSDN 博客](https://blog.csdn.net/dba_waterbin/article/details/8810339)
- [MySQL 线程处于 Waiting for table flush 的分析 - 潇湘隐者 - 博客园](http://www.cnblogs.com/kerrycode/p/7388968.html)
- [Get locked tables in mysql query - Stack Overflow](https://stackoverflow.com/questions/3230693/get-locked-tables-in-mysql-query)
- [MySQL 线程处于 Waiting for table flush 的分析 - 潇湘隐者 - 博客园](http://www.cnblogs.com/kerrycode/p/7388968.html)
- [mysql lock tables - 無執 - ITeye 博客](https://lhdst-163-com.iteye.com/blog/2117083)
- [[MySQL 案例]之备份与 Waiting for table flush | HiDBA | 林晓嘉](http://myrock.github.io/2014/11/20/mysql-waiting-for-table-flush/)
- [MySQL 的 SQL_CALC_FOUND_ROWS - 简书](https://www.jianshu.com/p/d676b763c747)

## 簡單小記

首先有看到 processlist 有一個`Waiting for table flush`
目前猜測可能是做 mysqldump 的時候，做 select 查詢可能會卡住
詳細原因還不清楚

### 備份 MySQL 方法

- [rsync 備份 | Tsung's Blog](https://blog.longwin.com.tw/2005/12/rsync_ssh_backup/)
- [在 Linux 上，關於 Mysql 備份，什麼方式最好呢？ tar 方式好久！ - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10091279)

感覺 rsync 指令好像滿好用的，改天來學一下如何用
