---
title: Oracle WHERE IN 條件塞入超過 500 筆查詢條件方法
date: 2021-01-12 21:04:10
tags: [oracle]
categories: Oracle
---

最近維運有時有超過 500 筆資料
需要在 SQL 指令區查詢
但是把多千筆資料拆開也是很耗時
[以固定行數切割文字檔案方法 | 程式狂想筆記](https://malagege.github.io/blog/2018/04/20/%E4%BB%A5%E5%9B%BA%E5%AE%9A%E8%A1%8C%E6%95%B8%E5%88%87%E5%89%B2%E6%96%87%E5%AD%97%E6%AA%94%E6%A1%88%E6%96%B9%E6%B3%95/)
先前有研究這個方法，可以快速切分
但直接查詢全部還是最快

<!--more-->


## 暫存表

- [昭佑.天翔: Oracle DB 中, 建立 Temporary Table](https://tomkuo139.blogspot.com/2009/12/oracle-db-temporary-table.html)
- [Oracle Temp Table 應用 @ Chuck Oracle Engineer :: 痞客邦 ::](https://roye0310.pixnet.net/blog/post/69218052)

正式環境無法使用

## 神奇大法


```sql
with t as 
(
    select (column_value).getnumberval() Codes from xmltable('1,2,3,4,5')
)
SELECT * FROM t
WHERE NOT EXISTS (SELECT 1 FROM M_ITEMS M WHERE codes = M.ITEM_CODE);
```
和
```sql
with t as 
(
    select (column_value).getstringval() Codes from xmltable('"A","B","C"')
)
SELECT * FROM t
WHERE NOT EXISTS (SELECT 1 FROM M_ITEMS M WHERE codes = M.ITEM_CODE);
```


主要重點是 `select (column_value).getnumberval() Codes from xmltable('1,2,3,4,5')`

參考: [sql - Oracle- create a temporary resultset for use in a query - Stack Overflow](https://stackoverflow.com/questions/12169842/oracle-create-a-temporary-resultset-for-use-in-a-query)


2021-03-13
## Oracle IN 可放超過1000查詢條件方法

雖然要手動組

```sql= 
select * from table where (1,ID)  in ( (1,0001),(1,0002)... (1,1999) )
```

參考:[oracle 1000 in clause limit @ 阿朗的部落格 :: 痞客邦 ::](https://copsonliu.pixnet.net/blog/post/92688453-oracle-1000-in-clause-limit)
