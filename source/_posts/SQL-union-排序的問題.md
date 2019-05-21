---
title: SQL union 排序的問題
date: 2019-05-20 23:58:50
tags: [sql, union]
categories: SQL
---

union 要做排序要放在最下面

select seq from ooo
union
select null as seq from xxx

seq 全部都會變成 null

最近太忙了
先記錄到這邊
有空在研究
[UNION 中 ORDER By 的使用 - 不要依赖这里 因为你有大脑 - ITeye 博客](https://wuaner.iteye.com/blog/559781)
