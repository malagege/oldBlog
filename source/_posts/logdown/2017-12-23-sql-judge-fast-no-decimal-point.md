---
layout: post
title: 'SQL判斷快否有小數點'
date: 2017-12-23 16:00
comments: true
categories: SQL
tags: SQL
---
今天要查詢商品價格有小數點的，爬一下Google
有查到不錯方法，記錄一下

<!--more-->


```sql
select   col1,
case   col1   when  cast(col1  as int) then '不是' else '是' end
from   表1
```

```sql
select   col1
from   表1
where col1 <> cast(col1 as int)
```

# 參考來源
[邏輯生活: SQL 判斷值是否有小數點](http://wushinetlife.blogspot.com/2012/02/sql_3749.html)