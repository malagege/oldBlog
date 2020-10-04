---
title: Oracle 時間運算 筆記
date: 2020-09-09 22:39:03
tags: [oracle]
categories: Oracle
---

TODO: 之後有空來整理

<!--more-->

昨天

```
AND oh.tran_date BETWEEN TRUNC(SYSDATE - 1) AND TRUNC(SYSDATE) - 1/86400
```
[sql - How can I select records ONLY from yesterday? - Stack Overflow](https://stackoverflow.com/questions/1574565/how-can-i-select-records-only-from-yesterday)