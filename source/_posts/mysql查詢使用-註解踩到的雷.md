---
title: mysql查詢使用--註解踩到的雷
date: 2018-04-18 20:35:51
tags: [mysql, comment, 註解]
categories: 程式心法
---

因為先前工作是使用DB2
不過...最近新的工作使用Mysql

發現竟然`--`註解不能用...

<!--more-->

Google一下，原來MySQL註解有3種

1. \#
2. /**/
3. \-\-   (但這種前後要留白)   ex : -- test

希望下次不要踩到一樣的雷XD
