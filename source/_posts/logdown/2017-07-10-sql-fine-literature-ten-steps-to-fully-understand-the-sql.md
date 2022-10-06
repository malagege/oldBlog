---
layout: post
title: 'SQL好文分享 - 十步完全理解 SQL'
date: 2017-07-10 13:18
comments: true
categories: SQL
tags: ['sql']
---
今天找SQL 的IN ,EXISTS,=ANY差別找到這篇文章 [十步完全理解 SQL](http://blog.jobbole.com/48369/)
看了覺得該簡討一下

<!--more-->
```sql
select a as named_column
from a_form
where named_column = 'c'
```

為什麼不能這樣用呢?
我之前聽到的是where抓不到這一層的
要再包一層才抓的到

```sql
select named_column
(select a as named_column
from a_form
)
where named_column = 'c'
```

直到我看到`2、 SQL 的语法并不按照语法顺序执行`
覺得我最基礎關念沒搞清楚(差點要跪鍵盤了....)

這篇文章真的不錯，值得我記錄
不過文章禁止轉載
>本文由 伯乐在线 - 水果泡腾片 翻译。未经许可，禁止转载！
英文出处： Lukas Eder 。欢迎加入翻译组。
http://blog.jobbole.com/55086/

在此記錄筆記

這篇看起來也不錯:P
http://blog.jobbole.com/48369/