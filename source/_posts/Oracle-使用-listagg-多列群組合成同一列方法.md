---
title: Oracle 使用 listagg 多列群組合成同一列方法
date: 2020-12-24 22:07:05
tags: [oracle, sql]
categories: SQL
---

之前有在 MySQL 用過這個需求用過這個需求[GROUP_CONCAT 和 CONCAT_WS 多個橫欄和縱欄組在一起方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/09/02/group-cancat-%E5%92%8C-concat-ws-%E5%A4%9A%E5%80%8B%E6%A9%AB%E6%AC%84%E5%92%8C%E7%B8%B1%E6%AC%84%E7%B5%84%E5%9C%A8%E4%B8%80%E8%B5%B7%E6%96%B9%E6%B3%95/)
但是 Oracle 用上有差異
還多了一堆限制(如:字數不能超過 4000)
distinct 還會被禁用??!

<!--more-->

## Distinct

1. [Oracle listagg去重distinct三種方法總結_weixiaohuai的博客-CSDN博客](https://blog.csdn.net/Weixiaohuai/article/details/84998212)
2. [Remove Duplicates From Comma Separated String — oracle-tech](https://community.oracle.com/tech/developers/discussion/2499711/remove-duplicates-from-comma-separated-string)

```sql
-- 此方法需要排序後才有過濾重複效果
select REGEXP_REPLACE('5,5,5,5,6,6,5,5,5,6,7,4,1,2,1,4,7,2', '(^|,)([^,]*)(,\2)+','\1\2') from dual;
---------------------------------
5,6,5,6,7,4,1,2,1,4,7,2
```

## 字數過長

```sql
SQL> select rtrim(xmlagg(xmlelement(e,id,',').extract('//text()') order by id).GetClobVal(),',')
   from (select level as id from dual connect by level < 1050)
  /

RTRIM(XMLAGG(XMLELEMENT(E,ID,',').EXTRACT('//TEXT()')ORDERBYID).GETCLOBVAL(),','
--------------------------------------------------------------------------------
1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18...
```

1. [ora-01489 字符串連接的結果過長 解決方案 - iUpoint - 博客園](https://www.cnblogs.com/iupoint/p/10974864.html)
2. [Oracle LISTAGG 長度大於 4000 解決方案 | lizhen's blog](https://lz5z.com/Oracle%20LISTAGG%E9%95%BF%E5%BA%A6%E5%A4%A7%E4%BA%8E4000%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/)

但這個沒有排序...

## 逗號排序

[Sort delimited separated values in a string with (Oracle) SQL - Stack Overflow](https://stackoverflow.com/questions/7808325/sort-delimited-separated-values-in-a-string-with-oracle-sql)

```sql
select listagg(somedata, '|') within group (order by somedata) somedata from (
with q as (select '|'||'9|2|6|7|5' as somedata from dual)
select substr(somedata, instr(somedata, '|', 1, rownum) + 1, 1) somedata
  from q,
   (select 1 from q connect by level <= length(regexp_replace(somedata, '[0-9]', '')))
)

```
但這個跟 xmlagg 結合會有問題
都會跑出 null


先記錄到這邊