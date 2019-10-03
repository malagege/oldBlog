---
title: MySql Lock wait timeout exceeded 小記
date: 2019-09-24 20:53:17
tags: [mysql]
categories: MySQL
---

今天在 log 發現有少數 SQL 會 MySql Lock wait timeout exceeded
網路找幾天文章，先暫時放著

[MySql Lock wait timeout exceeded该如何处理？ | 凝雨 - Yun | 快乐编程每一天 - Happy Coding Every Days](https://ningyu1.github.io/blog/20180408/75-mysql-lock-wait-timeout-exceeded.html)
[[心得] 高併發伺服器設定 « Huli's Blog](http://huli.logdown.com/posts/542398-high-connection-setting)
[谈谈MySQL的锁 - 蹲厕所的熊](https://benjaminwhx.com/2018/04/27/%E8%B0%88%E8%B0%88MySQL%E7%9A%84%E9%94%81/)
[在laravel上處理高併發問題 - ScottChayaa](https://blog.scottchayaa.com/post/2019/01/09/how-to-handle-the-high-concurrency-on-laravel/)
> mysql transaction default timeout is 50 sec => my suggestion is between 3 ~ 5 sec
我同事也是建議不要設長

[MySQL insert into ... select 的锁情况 【转】 - 大树叶 技术专栏 - CSDN博客](https://blog.csdn.net/bigtree_3721/article/details/73277419)
[MySQL InnoDB 锁等待和锁等待超时的处理 - 简书](https://www.jianshu.com/p/4bf87a2f83ae)
[資料庫交易的 Isolation - getamis - Medium](https://medium.com/getamis/database-transaction-isolation-a1e448a7736e)
[對於 MySQL Repeatable Read Isolation 常見的三個誤解 - Chester Chu - Medium](https://medium.com/@chester.yw.chu/%E5%B0%8D%E6%96%BC-mysql-repeatable-read-isolation-%E5%B8%B8%E8%A6%8B%E7%9A%84%E4%B8%89%E5%80%8B%E8%AA%A4%E8%A7%A3-7a9afbac65af)
[[SQL]建立Index來避免DeadLock | 亂馬客 - 點部落](https://dotblogs.com.tw/rainmaker/2014/05/28/145284)
[insert into a select * from b 锁问题分析 | 没有伞的孩子必须努力奔跑](http://www.xuchanggang.cn/archives/1188.html)
