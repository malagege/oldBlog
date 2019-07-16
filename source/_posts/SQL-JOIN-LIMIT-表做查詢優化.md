---
title: SQL JOIN LIMIT 表做查詢優化
date: 2019-07-10 00:42:44
tags: [sql, join]
categories: SQL
---

最近公司上線 APP 的某一筆資料原本要做例外排除
但是沒有做
因為這一筆資料會撈出過多的方法
APP 已經審查過
所以沒辦法更動 APP
只能看有沒有比較優的查詢方法
但是有網路找到一個方法

<!--more-->

之前就有看過類似方法
不過都沒有紀錄
今天遇到，原本想說很複雜的 SQL 可能沒有什麼效果
但是今天用了一下
還不錯

> SELECT \* FROM test AS t
> INNER JOIN ( SELECT id FROM test ORDER BY post_time DESC LIMIT 15000,10 ) AS s
> ON t.id=s.id
> 我相信 , 這道指令應該 1 秒都不用
> 為何會如此呢 ?
> 因為我們在 subquery 中只有抓 id , 這樣 mysql 就只需要少量記憶體就可以存放資料列進行排序 , 原始的作法就是每一列有多少資料 , mysql 就必須分配多少記憶體 , 如果資料數太大 , 記憶體不夠分配 , 自然就是用 disk 了
> 但如果我們要抓的資料只是前面幾筆 , 例如 LIMIT 100,10 , 那麼原本的寫法會比較快 , 因此這可以在程式中判斷而交互使用兩種 QUERY 的方式
> [善用 sub query 優化 order by limit | 壞蛋的密室](https://www.pigo.idv.tw/archives/390)

```sql
SELECT
xxx
FROM xxx x1
INNER JOIN  s ON x1.id = s.id
...
LEFT JOIN  pl ON pl.id = x1.id
INNER JOIN (SELECT id FROM pl pl2 WHERE xxx_id IN (
select oo FROM ss where oo.id = ___ AND o.condition1 = 1
AND o.condition2 = 1
) ORDER BY counts desc limit 0,500) as a ON pl.id = a.id
WHERE s.id = ___
AND ...
ORDER BY pl.counts DESC
LIMIT 0, 500
```

沒想到這麼複雜 SQL 也可以這麼快
