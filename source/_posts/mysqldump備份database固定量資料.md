---
title: mysqldump備份database固定量資料
date: 2019-04-25 21:04:05
tags: [mysqldump, mysql]
categories: MySQL
---

最近要本機測試資料
但是要如何不中斷服務又能快速備份的方法
最後想到有 mysqldump

<!--more-->

之前有想過 mysqldump 是 DBA 才會用到工具
但我又不需要全部備份
不知道有沒有什麼快速匯出但是數量不想要太多
最後有找到很快的方法

## 使用方法

`mysqldump -uroot -p123 test > c:\test.sql`
**注意-u 和-p 後面帳號密碼要連在一起**
參考:[鯉魚智缺熊的網智: windows 下使用 command line dump & import mysql](http://koibear.blogspot.com/2012/11/windowscommand-line-dump-import-mysql.html)

但注意!!用 mysqldump 是會 lock DB 的

> MyISAM tables require this locking because they don't support transactions. However, InnoDB (the default storage engine as of MySQL 5.5.5) supports transactions. Mysqldump defaults to a conservative setting of locking everything, but we don't need to use that default - we an avoid locking tables completely.
> [Mysqldump with Modern MySQL | Servers for Hackers](https://serversforhackers.com/c/mysqldump-with-modern-mysql)

## 限制每個 table 數量

```bash
mysqldump -u [username] -p [dbname] {--opt} --where="true limit 100" > dump.sql
```

[mysql dump of talble limit 100 - Server Fault](https://serverfault.com/questions/173834/mysql-dump-of-talble-limit-100)

> 如果運行 mysqldump 沒有--quick 或--opt 選項，mysqldump 在轉儲結果前將整個結果集裝入內存。如果轉儲大資料庫可能會出現問題。該選項預設啟用，但可以用--skip-opt 禁用。

[Jax 的工作紀錄: (轉載)mysqldump 5.1 資料備份詳細指令 [MySQL]](https://jax-work-archive.blogspot.com/2009/07/mysqldump-51-mysql.html)

> –opt 　　　是 mysqldump 中的預設參數，參數功能相等於加入了 –add-drop-table –add-locks –create-options –disable-keys –extended-insert –-lock-tables –quick –set-charset 等參數

[[MySQL]利用 mysqldump 備份 – FunMing 基地](https://fmbase.tw/blog/2013/01/23/mysql%E5%88%A9%E7%94%A8mysqldump%E5%82%99%E4%BB%BD/)
[FAQ 系列 | mysqldump 选项之 skip-opt-老叶茶馆-51CTO 博客](https://blog.51cto.com/imysql/1879753)

## 鎖表解決方法

解決方法是用`–skip-lock-tables`

> 一、mysqldump 在備份數據庫時鎖表的問題
> 在使用'mysqldump'工具在備份庫表時，會對庫表進行鎖定，這會對業務的運行產生一定的影響；對於鎖表的過程及解決方法，作者也談談自己的認識。
> 緩解鎖定的方法：
> 1、Use --skip-lock-tables mysqldump option. Your tables won't be locked, but your backup might be not consistent (depends on your schema: if your database transactions touch several tables at once, and one table is already backed up and other is not. For example: two tables: customers and orders. If customers is backed up first, and then new customer/order pair is inserted, you might end up with order without customer in your backup).
> This problem is primary reason why mysqldump locks all tables by default. If this issue does not apply to you, skipping the table locking in mysql is the most simple solution.
> 會影響數據的一致性(可能比丟數據還要遭糕)，故不推薦使用這個方法。
> 2、Use some other backup method. For example: Flush tables with read lock, create LVM snapshot, unlock tables, mount your LVM snapshot and back up your data. This gets quickly, much more complex than simple mysqldump.
> If your MySQL data is not on a LVM volume, you can create a slave MySQL server and back it up, without touching the master, so locking occurs on the slave and master is always free.
> 可以參考「lvm 備份 mysql 數據庫參考」，這篇文章。

[mysqldump 在备份数据库的问题集-FreeOA](http://www.freeoa.net/osuport/db/mysqldump-lock-on-backup_1453.html)

## 最後 SQL

```bash
mysqldump -uroot -p123 test –skip-lock-tables  > c:\test.sql
```

其他參考來源:
[mysqldump 与 innobackupex 备份过程你知多少 - xiaoboluo768 - 博客园](https://www.cnblogs.com/xiaoboluo768/p/7560105.html)
[MySQL Database Export (100 Rows from each table in a database) - Stack Overflow](https://stackoverflow.com/questions/38495460/mysql-database-export-100-rows-from-each-table-in-a-database) <---這個我沒有嘗試
[使用 mysqldump 倒出 MySQL 所有資料庫(表) 固定筆數的資料 | Tsung's Blog](https://blog.longwin.com.tw/2018/08/mysql-database-mysqldump-dump-sample-dataset-2018/)
