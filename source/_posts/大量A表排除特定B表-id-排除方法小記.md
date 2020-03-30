---
title: 大量A表排除特定B表 id 排除方法小記
date: 2020-02-19 22:44:55
tags: [mysql, sql]
categories: SQL
---

最近需求需要A表過濾B表
但是資料量非常大
處理時間非常久

<!--more-->

## 刪除方法得到結果

```sql
SELECT id FROM A  LEFT JOIN B ON A.id = B.id WHERE B.id is null 
-- 執行非常久
-- 後來想嘗試用刪除資料看看結果，資料表都是暫時的

DELETE FROM A WHERE A.id IN (SELECT id FROM B)
-- 執行非常久

DELETE a FROM A a INNER JOIN B b ON a.id = b.id
-- 執行非常快

```
之前有紀錄[SQL UPDATE 和 DELETE 趴配 JOIN 方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/08/16/SQL-UPDATE-%E5%92%8C-DELETE-%E8%B6%B4%E9%85%8D-JOIN-%E6%96%B9%E6%B3%95/)

最後我使用上面方式解決這個方法

## 使用 NOT EXISTS

其實以前就有聽過 EXISTS 效能是不錯的
剛好爬文看到[LEFT JOIN / IS NULL vs. NOT IN vs. NOT EXISTS: nullable columns at EXPLAIN EXTENDED](https://explainextended.com/2010/05/27/left-join-is-null-vs-not-in-vs-not-exists-nullable-columns/)
看到使用 NOT EXISTS 還滿不錯的
但是做 `count(distinct)` 還是很慢
group by 也不快

```sql
SELECT id FROM A  LEFT JOIN B ON A.id = B.id WHERE B.id is null 
```

又空想到好的方式在做筆記

## 可以在探討方法

### pandas

[Python之使用Pandas庫實現MySQL資料庫的讀寫 | 程式前沿](https://codertw.com/%E8%B3%87%E6%96%99%E5%BA%AB/16156/)
[Pandas 加载数据的I/O性能比较 - This is Robert Lexis.](https://robertlexis.github.io/2017/11/21/Pandas-%E5%8A%A0%E8%BD%BD%E6%95%B0%E6%8D%AE%E7%9A%84I-O%E6%80%A7%E8%83%BD%E6%AF%94%E8%BE%83/)
[[Pandas]一个Pandas VS MySQL SQL 的性能对比 - FlyML](https://www.flyml.net/2019/03/25/pandas%e4%b8%80%e4%b8%aapandas-vs-mysql-sql-%e7%9a%84%e6%80%a7%e8%83%bd%e5%af%b9%e6%af%94/)

### SQL

[LEFT JOIN / IS NULL vs. NOT IN vs. NOT EXISTS: nullable columns at EXPLAIN EXTENDED](https://explainextended.com/2010/05/27/left-join-is-null-vs-not-in-vs-not-exists-nullable-columns/)
[SQL好文分享 - 十步完全理解 SQL | 程式狂想筆記](https://malagege.github.io/blog/2017/07/10/logdown/2017-07-10-sql-fine-literature-ten-steps-to-fully-understand-the-sql/)
[經典：十步完全理解 SQL-知識星球](http://www.ipshop.xyz/12766.html)


**2020-03-05**

> 通過使用EXISTS，Oracle會首先檢查主查詢，然後執行子查詢直到它找到第一個匹配項，這就節省了時間。Oracle在執行IN子查詢時，首先執行子查詢，並將獲得的結果列表存放在一個加了索引的臨時表中。在執行子查詢之前，系統先將主查詢掛起，待子查詢執行完畢，存放在臨時表中以後再執行主查詢。這也就是使用EXISTS比使用IN通常查詢速度快的原因

[SQL中EXISTS與IN的效率問題 - IT閱讀](https://www.itread01.com/content/1547569286.html)

通常 exists 子查詢是查到有資料才會出來
這邊要特別注意