---
title: SQL UPDATE 和 DELETE 趴配 JOIN 方法
date: 2019-08-16 20:42:28
tags: [mysql, insert, delete]
categories: MySQL
---

最近看到 UPDATE 和 DELETE
可以搭配用 JOIN 方式
這種常常查詢資料都要搭配程式 for 迴圈實作
我覺得非常實用

<!--more-->

我網路隨便找一個範例
[SQL Fiddle](http://sqlfiddle.com/#!9/86b033/1)

## UPDATE ... INNER JOIN

```SQL
UPDATE comentarios a
       INNER JOIN respuestas b
               ON a.comentario_servicio = b.respuesta_servicio
SET    a.comentario_servicio = 'fff',
       b.respuesta_servicio = 'ccc'
WHERE  b.respuesta_servicio = 'T.I'
```

透過 `JOIN` 方式可以同時改兩張表的資料
但還有更神奇的妙用
就是把`b`表換成子查詢
可以用 JOIN 方式改變`a`表的內容
省了寫程式 code
**超級實用**

```SQL
UPDATE comentarios a
       INNER JOIN (SELECT
          xxx,
          if(xxx,ooo,bbb) as set_value
          FROM ....
          ....
          ...
       ) b
        ON a.comentario_servicio = b.respuesta_servicio
SET    a.comentario_servicio = set_value
WHERE  b.respuesta_servicio = 'T.I'
```

## DELETE ... INNER JOIN

DELETE 也能搭配 JOIN
不過有點跟原本 DELETE 不太一樣

如下

```sql
DELETE a,b
FROM       comentarios a
INNER JOIN respuestas b
ON         a.comentario_servicio = b.respuesta_servicio
WHERE      b.respuesta_servicio = 'T.I';
```

通常穩們用的 DELETE 都是改`from`表
但 JOIN 不是這樣，是針對 DELETE 後面 table 命名 a,b 做刪除
當然也可以不用刪除兩張表
`DELETE a FROM ...`
或
`DELETE b FROM ...`
都可以指定刪除

此招也很實用

## 鎖表的問題

上面妙招還是會有一些缺點
SQL 鎖表對一些資料表會造成些問題
有爬到[mysql insert 锁机制【转】 - paul_hch - 博客园](https://www.cnblogs.com/paul8339/p/6877729.html) {% asset_link web1.png 備份圖  %}

## 其他

SQL 也有一些實用用法

```sql
INSERT INTO xxx SELECT...
```

```sql(mysql)
REPLACE INTO ....
```

```sql
INSERT INTO .... ON DUPLICATE KEY UPDATE
```

[（mysql）replace into ...与 insert into ... on duplicate key update 对比分析 - Abysscarry 的博客 - CSDN 博客](https://blog.csdn.net/abysscarry/article/details/80518278)
[mysql 中 replace into 与 insert into on duplicate key update 的使用和不同点 - 怀素真 - 博客园](https://www.cnblogs.com/jkko123/p/7285641.html)
[MySQL 的 Replace into 与 Insert into on duplicate key update 真正的不同之处 - xlxxcc 的专栏 - CSDN 博客](https://blog.csdn.net/xlxxcc/article/details/52439734)
