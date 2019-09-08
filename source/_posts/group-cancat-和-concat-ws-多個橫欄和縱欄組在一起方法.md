---
title: GROUP_CONCAT 和 CONCAT_WS 多個橫欄和縱欄組在一起方法
date: 2019-09-02 21:15:45
tags: [mysql]
categories: MySQL
---

之前有使用 group_concat ，但沒有特別做筆記
今天剛好查報表資料，就特別來筆記

<!--more-->


```sql
SELECT oa.id,
       GROUP_CONCAT(if(a.xxx_group = 0 , NULL,a.xxx_group)) AS xxx_group,
       CONCAT_WS(',',IF(f.a1,'a1',NULL),IF(f.a2,'a2',NULL),IF(f.a3,'a3',NULL)) AS 'a...',
       IF(a.xxx > ''
          OR a.bbb > '','Y','N') AS 'oo',
       IF(a.cc > '' ,'Y','N') AS 'cc'
FROM a
LEFT JOIN b ON b = a.id
LEFT JOIN f ON f.xxx_id = a.id
```

## GROUP_CONCAT

當初在 MySQL 看到這個函數有點大開眼界
因為 DB2 沒有看到類似的 function
但後來版本也有類似功能 [DB2 comma separated output - Stack Overflow](https://stackoverflow.com/questions/7188542/db2-comma-separated-output)

簡單說明一下 GROUP_CONCAT 
當初看到這個函數想說他能不能推回去(多列)
但是這不可能做到 [MySQL :: MySQL 8.0 Reference Manual :: 12.20.1 Aggregate (GROUP BY) Function Descriptions](https://dev.mysql.com/doc/refman/8.0/en/group-by-functions.html)
看到 GROUP_CONCAT 歸類到 GROUP FUNCTION 我覺得不太可能...

```
GROUP_CONCAT([DISTINCT] expr [,expr ...]
[ORDER BY {unsigned_integer | col_name | expr}
[ASC | DESC] [,col_name ...]]
[SEPARATOR str_val])
```
使用搭配記得要加 GROUP BY

但不知道有兩個欄位做 GROUP_CONCAT 不知道會不會有問題
改天再實驗

## CONCAT_WS

之前看到這個函示不知道這個用在哪裡
剛好資料要抓多欄資料合併成一欄...
我想到用`CONCAT_WS`
我裡面包個IF 做 null判斷是預防抓到空字串轉`null`動作
轉null後就不會串到`,`裡面



