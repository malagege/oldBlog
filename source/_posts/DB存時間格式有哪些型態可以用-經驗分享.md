---
title: DB存時間格式有哪些型態可以用(經驗分享)
date: 2019-02-18 21:48:51
tags: [mysql]
categories: MySQL
---

之前不同公司 db 使用型態都不太一樣
前一間公司試用`varchar`
現在是用 datetime 預設值`0000-00-00`
最近在想這個預設值有甚麼好處

<!--more-->

首先，一直搞不清楚不用 Null，印象中 Null 在做 Index 不是很好
我自己想出結論，這個可能可以優化 Index key
'0000-00-00'大部分跟 NULL 一樣(https://dev.mysql.com/doc/refman/8.0/en/using-date.html)
但是看到 MySQL 之後版本預設 sql_mode 有 NO_ZERO_IN_DATE,NO_ZERO_DATE
不能使用'0000-00-00'，感覺 MySQL 官方預設都不建議用'0000-00-00'
我有特別 Google 一下，感覺兩種都有人在用

- [Datetime NULL or 0000-00-00 00:00:00 - Databases - The SitePoint Forums](https://www.sitepoint.com/community/t/datetime-null-or-0000-00-00-00-00-00/49216)
- [mysql - default date '0000-00-00 00:00:00' or null - Stack Overflow](https://stackoverflow.com/questions/12482560/default-date-0000-00-00-000000-or-null?fbclid=IwAR3OymTlPF517hx7lWUii1rvWgoVN__N3w12Gds3L9cyyPmnzIWP8XSNfUk)

感覺大多留言是偏向使用`null`

不過，我有去社群問問大家[Backend 台灣 (Backend Tw)](https://www.facebook.com/groups/616369245163622/permalink/1554464451354092/)

> int / timestamp/ datetime 這三種來記錄
> 在大量資料來做搜索時(沒索引)
> 效能是 int > UNIX_TIMESTAMP > timestamp > datetime
> 有索引
> 效能是 UNIX_TIMESTAMP > int > timestamp > datetime

> 一般用框架的，都是 timestamp ，而且沒有就是 0，這樣子最快，只是需要轉換而已
