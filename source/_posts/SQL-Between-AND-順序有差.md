---
title: SQL Between ... AND ...  順序有差
date: 2020-09-08 23:27:01
tags: [sql,between]
categories: SQL
---

今天寫 Oracle 時候發現 between sysdate and sysdate -1 竟然不能查詢
原來看一下文件，順序真的是有差的

<!--more-->

## Oracle

> is the value of the boolean expression:
> expr2 <= expr1 AND expr1 <= expr3
> If expr3 < expr2, then the interval is empty. If expr1 is NULL, then the result is NULL. If expr1 is not NULL, then the value is FALSE in the ordinary case and TRUE when the keyword NOT is used.

[Oracle BETWEEN Conditions](https://docs.oracle.com/cd/B28359_01/server.111/b28286/conditions011.htm#SQLRF52147)

## MySQL

> If expr is greater than or equal to min and expr is less than or equal to max, BETWEEN returns 1, otherwise it returns 0. This is equivalent to the expression (min <= expr AND expr <= max) if all the arguments are of the same type. Otherwise type conversion takes place according to the rules described in Section 12.3, “Type Conversion in Expression Evaluation”, but applied to all the three arguments. 

[MySQL :: MySQL 8.0 Reference Manual :: 12.4.2 Comparison Functions and Operators](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_between)
