---
title: Dbeaver 使用變數 做SQL 範本 和查詢設定 title
date: 2021-07-14 20:43:41
tags: [dbeaver, sql]
categories: 實用工具
---

Dbeaver 使用變數 做SQL 範本 和查詢設定 title

<!--more-->

## Dbeaver 使用變數

```sql
@set a = '1'
@set b = '1'


SELECT :a FROM dual;

SELECT :b FROM dual;
```
按下![](https://i.imgur.com/atqu9d0.png)

可查多個查詢
![](https://i.imgur.com/9zh6NIk.png)


### sql developer  使用變數方法(番外)

```sql
define abc=1;
select * from dual where rownum=&abc;
```

## 設定查詢標籤

一般我們維運不可能只查一次SQL
多個SQL執行，標籤不好分辨是什麼
我有查到使用方式

```sql
@set a = '1'
@set b = '1'

-- title: test1
SELECT :a FROM dual;

-- title: test2
SELECT :b FROM dual;
```

![](https://i.imgur.com/PGldd2H.png)



參考: [mysql - Is there a way to define the names of the result tabs names in an SQL Script in DBeaver? - Stack Overflow](https://stackoverflow.com/questions/47081804/is-there-a-way-to-define-the-names-of-the-result-tabs-names-in-an-sql-script-in)