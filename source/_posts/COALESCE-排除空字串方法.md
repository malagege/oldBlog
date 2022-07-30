---
title: COALESCE 排除空字串方法
date: 2022-07-31 02:07:56
tags: [sql,coalesce]
categories: SQL
---

之前有寫過[mysql COALESCE 取代null值好方法 | 程式狂想筆記](https://malagege.github.io/blog/2018/07/19/mysql-COALESCE-%E5%8F%96%E4%BB%A3null%E5%80%BC%E5%A5%BD%E6%96%B9%E6%B3%95/)
最近遇到筆記一下。

<!--more-->

```sql=
SELECT COALESCE(NULLIF(Other,''),Industry) Ind FROM registration
```

[sql server - SQL Coalesce with empty string - Stack Overflow](https://stackoverflow.com/questions/17283978/sql-coalesce-with-empty-string)