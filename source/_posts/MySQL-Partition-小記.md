---
title: MySQL Partition 小記
date: 2019-06-25 22:20:58
tags: [mysql, partition]
categories: MySQL
---

最近接觸一點 Partition
但這個東西不是很熟悉
所以小記一些東西(都是網路上找的)

<!--more-->

## 主鍵和 Parition

`1503 - A PRIMARY KEY must include all columns in the table's partitioning function`
[MYSQL 的分区字段，必须包含在主键字段内 - 王 庆 - 博客园](https://www.cnblogs.com/zhishan/p/3285055.html)

要把 patition 的欄位加到 PRIMARY KEY

[MySQL 之 KEY 分區引發的血案 - 每日頭條](https://kknews.cc/other/mjzoq52.html)

## SQL 查詢 Partition 語法

```sql
SELECT * FROM information_schema.partitions WHERE TABLE_SCHEMA='your_database' AND TABLE_NAME = 'your_table' AND PARTITION_NAME IS NOT NULL
```

[Are the MySQL Partitions visible in the EXPLAIN plan of a SELECT....? - Stack Overflow](https://stackoverflow.com/questions/8700613/are-the-mysql-partitions-visible-in-the-explain-plan-of-a-select)

## EXPLAIN 沒有出現 Partition

用`EXPLAIN PARTITIONS`

[Are the MySQL Partitions visible in the EXPLAIN plan of a SELECT....? - Stack Overflow](https://stackoverflow.com/questions/8700613/are-the-mysql-partitions-visible-in-the-explain-plan-of-a-select)

[mysql - How to view information of partitions in a table? - Stack Overflow](https://stackoverflow.com/questions/25508419/how-to-view-information-of-partitions-in-a-table)

## Partition 分配不均的問題

用質數，我也沒有實驗過...
[MySQL 之 KEY 分區引發的血案 - 每日頭條](https://kknews.cc/other/mjzoq52.html) {% asset_link 2.png 備份圖 %}
[mysql 中的 key 分区为什么总是一半有数据，一半没有数据？-CSDN 论坛](https://bbs.csdn.net/topics/390857704)

```
如果設置40，64，128等偶數個分區數（PARTITIONS 64），會導致編號為奇數的分區（p1, p3, p5, p7, ... p2n-1）完全插不進數據；
如果設置63，121（PARTITIONS 63）這種奇數但非質數個分區數，所有分區都會有數據，但是不均勻；
如果設置137，31這種質數個分區數（PARTITIONS 137），所有分區都會有數據，並且非常均勻；


原文網址：https://kknews.cc/other/mjzoq52.html
```

## KEY & Hash 差異

[MySQL 分区总结 - iVictor - 博客园](https://www.cnblogs.com/ivictor/p/5033708.html)

看不是很懂，Hash 不能重覆,Key 可以??? 等研究出來再補

## 手建和內建 Partition 差別

> 手工分表 VS 分區表
> 手工分表的邏輯，找到所有需要更新的分表，然後依次更新，在性能上，與分區表並沒有實質的差別
> 分區表由 Server 層決定使用哪個分區，手工分表由應用代碼決定使用哪一個分表

[MySQL -- 分区表 | 点滴积累](http://zhongmingmao.me/2019/03/17/mysql-partition-table/) {% asset_link 1.png 備份圖 %}

## UUID 主鍵 vs 流水號 主鍵

[MySQL 使用自增 ID 主键和 UUID 作为主键的优劣比较详细过程（从百万到千万表记录测试） - chuixue24 的博客 - CSDN 博客](https://blog.csdn.net/chuixue24/article/details/90449075)

## 不是每張表適合做 Partition

也看 SQL 語句，通常要看所有 WHERE 條件資料依照做切割

## 刪除 Partition 會有資料消失??!

phpmyadmin 可以做刪除動作!!但發現會有少資料

解決方法

```SQL
ALTER TABLE xxxxxx rename TO old_20170101_xxxxxx;

CREATE TABLE `xxxxxx` (
ooooo
) ENGINE=InnoDB DEFAULT CHARSET=utf8
;

INSERT INTO xxxxxx SELECT * FROM old_20170101_xxxxxx;
```

不過為什麼會少資料還沒有找到原因
