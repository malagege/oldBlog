---
title: mysql字串自動轉數字小記
date: 2019-01-17 20:49:43
tags:
categories:
---

最近用 MySQL 做時間比較
想做`datetime欄位(0000-00-00 00:00:00) > ''`
結果得出來結果是`0`

<!--more-->

之前知道 SQL 沒有 boolean
不過在做時間比較
發現很奇怪
怎麼跟空字串比較結果為`0`

<!--more-->

Google 一下
發現原來 MySQL 會做自動轉型

`0 = ''`為`1`
原來空字串會自動轉成`0`
所以才會得出這個結果
這個不常做，感覺很容易忘記

2019-06-10

最近發現 null 時候，做 where 條件(大於、小於)不會抓到
但是 0000-00-00 可以
所以這是為什麼大家都會這樣用?!
但這樣做 add_date 時候就會有問題
有優點也有缺點

參考來源：

- [mysql: why comparing a 'string' to 0 gives true? - Stack Overflow](https://stackoverflow.com/questions/22080382/mysql-why-comparing-a-string-to-0-gives-true)
- [MySQL :: MySQL 8.0 Reference Manual :: 12.3.2 Comparison Functions and Operators](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_equal)
- [type conversion - MySQL automatically cast/convert a string to a number? - Stack Overflow](https://stackoverflow.com/questions/21762075/mysql-automatically-cast-convert-a-string-to-a-number)
- [MySQL :: MySQL 5.7 Reference Manual :: 12.2 Type Conversion in Expression Evaluation](https://dev.mysql.com/doc/refman/5.7/en/type-conversion.html)
