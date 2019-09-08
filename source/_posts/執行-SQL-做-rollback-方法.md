---
title: 執行 SQL 做 rollback 方法 & 訂單編號高併發的小記
date: 2019-09-08 18:00:05
tags: [sql]
categories: SQL
---

最近在想 phpmyadmin 到底能不能做到錯誤時 rollback
先講結論...應該是不行的
執行 phpmyadmin 時候好像沒被法判斷有沒有錯誤(指令的好像也一樣...)
有什麼新發現，之後再修改

<!--more-->

我覺得最佳實例應該要用類似 DB client 執行
手動切換交易模式(把 autocommit 關掉)

執行每一段 SQL 後，確定 SQL 都執行正確

依照結果手動下 commit 或 rollback 
改資料前可以先備份資料

stored procedure 可以做到
但因為沒什麼機會寫這個，所以先留著
[sql - MySQL : transaction within a stored procedure - Stack Overflow](https://stackoverflow.com/questions/9974325/mysql-transaction-within-a-stored-procedure)
[sql - try....catch in mysql for transaction? - Stack Overflow](https://stackoverflow.com/questions/30973002/try-catch-in-mysql-for-transaction)

## isnert 失敗 auto_increment

```sql
ALTER TABLE tbl auto_increment = 1
```
但有部分答案建議不要調整 auto_increment
[MySQL Auto Increment Columns on TRANSACTION, COMMIT, and ROLLBACK - Stack Overflow](https://stackoverflow.com/questions/14758625/mysql-auto-increment-columns-on-transaction-commit-and-rollback)
[transactions - MySQL AUTO_INCREMENT does not ROLLBACK - Stack Overflow](https://stackoverflow.com/questions/449346/mysql-auto-increment-does-not-rollback)

後來我想到 insert into xxx values( select max(xxx) + 1 from xxx,'test' ); 會不會有高併發的問題 (其實很多程式都這樣寫)
之前類似寫過 [MySQL(MariaDB) insert有關做相同表會遇到問題小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/06/02/MySQL-MariaDB-insert%E6%9C%89%E9%97%9C%E5%81%9A%E7%9B%B8%E5%90%8C%E8%A1%A8%E6%9C%83%E9%81%87%E5%88%B0%E5%95%8F%E9%A1%8C%E5%B0%8F%E8%A8%98/)

## 如何生產唯一訂單編號

額外新開一個 table ，做訂單編號處理
用 `SELECT ... FOR UPDATR`處理
一直看到公司有看到商品編號額外開了一個 table
現在有一種恍然大悟的感覺
但開發者沒有加 `FOR UPDATE` 後來的 commit 有補上 `FOR UPDATE`
相關 for update 處理
> [用 SELECT ... FOR UPDATE 避免 Race condition @ Vexed's Blog :: 隨意窩 Xuite日誌](https://blog.xuite.net/vexed/tech/22289223-%E7%94%A8+SELECT+...+FOR+UPDATE+%E9%81%BF%E5%85%8D+Race+condition)
> [MySQL 在高并发下的 订单撮合 系统使用 共享锁 与 排他锁 保证数据一致性 - 指尖下的幽灵 - 博客园](https://www.cnblogs.com/linguanh/p/10019002.html)

更好的方法還有透過 redis 去做
[java web系统在高并发下如何实现订单号生成唯一？ - 知乎](https://www.zhihu.com/question/21128632) {% asset_link web1.png 備份圖 %}
[Redis 高并发问题,及解决方案！ - 简书](https://www.jianshu.com/p/6ce40dcbf3fe) {% asset_link web2.png 備份圖 %}
[分布式架构系统生成全局唯一序列号的一个思路 - 知乎](https://zhuanlan.zhihu.com/p/32657546) {% asset_link web3.png 備份圖 %}

其實這篇我原本主要是要紀錄 sql file 做 rollback
但沒想到...從`isnert 失敗 auto_increment`到`如何生產唯一訂單編號`我覺得非常重要
就先記錄了

## mysql 執行 .sql

[How to Run SQL Text File on MySQL Command Prompt – TecAdmin](https://tecadmin.net/run-sql-text-file-on-mysql-command-prompt/)