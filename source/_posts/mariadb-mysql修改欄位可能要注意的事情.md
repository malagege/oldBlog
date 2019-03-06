---
title: mariadb/mysql修改欄位可能要注意的事情
date: 2019-02-18 21:13:53
tags:
categories:
---

最近修改 null 欄位改成非 null 欄位
在測試環境上修改欄位通過
但到正式環境就沒通過
卡了很久整理一下，一些問題

<!--more-->

## MySQL、MariaDB

在一般實作 int(null)->int(not null)~~，null 值都會自動轉乘`0`~~，舊版可能會自動轉，MySQL 8.0 不會自動轉，會報`Invalid use of NULL value` varchar,datetime(null) ->(not null)，會報錯誤 `Invalid use of NULL value`

但 int 有找到下
[MySQL alter table modify column failing at rows with null values - Stack Overflow](https://stackoverflow.com/questions/7828440/mysql-alter-table-modify-column-failing-at-rows-with-null-values)

[MySQL 5.7 預設 SQL 模式帶來的問題總結 | 程式前沿](https://codertw.com/%E8%B3%87%E6%96%99%E5%BA%AB/122336/)
[XYZ 的筆記本: MySQL 資料表分區(partition)](https://xyz.cinc.biz/2015/07/mysql-partition.html)

## ignore

`alter ignore table`
[MySQL alter table modify column failing at rows with null values - Stack Overflow](https://stackoverflow.com/questions/7828440/mysql-alter-table-modify-column-failing-at-rows-with-null-values)
不過剛剛發現 MySQL 5.7 這個就不能用了...
MariaDB 還可以
varchar(null) -> 空字串

還發現很奇怪的是，MySQL 到後面版本有`NO_ZERO_IN_DATE，NO_ZERO_DATE，TRADITIONAL`

所以可能還是需要自己手動把 null 改成有值
雖然`ignore`好像能處理
但有可能會早成資料遺失的可能(修改 index)

測試 sql

```sql
DROP TABLE IF EXISTS `test`;
CREATE TABLE `test` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `a` varchar(10) DEFAULT NULL,
  `b` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

INSERT INTO `test` (`id`, `a`, `b`) VALUES
(1,	NULL,	NULL);

-- 2019-02-18 13:06:14
```

可自己嘗試修改 a,b 欄位測試看看

其他資料
[alter ignore table 添加 unique key - 菠萝大数据梦工厂（Free World） - CSDN 博客](https://blog.csdn.net/jiangshouzhuang/article/details/43925763)
[MySQL 刪除重複的資料,只留下一筆資料 | 我的 PHP 冒險之路](http://play-php.blogspot.com/2016/06/mysql.html)
[MySQL 5.7 預設 SQL 模式帶來的問題總結 | 程式前沿](https://codertw.com/%E8%B3%87%E6%96%99%E5%BA%AB/122336/)
