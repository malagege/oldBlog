---
title: MySQL 高併發問題解決小記
date: 2019-06-01 23:56:14
tags: [mysql, lock]
categories: MySQL
---

小記...，有時間補

<!--more-->

# 設計資料表 unique

這個是我想的，不過網路上感覺很少寫用這個方法
但還是有找到少數人有在討論
好像滿可行的

用 DB unique 可以預防同時資料問題
或者有限制 enum=[1,2,3]
每次資料進來可預防這個問題
但是做刪除，在加入可能就比較麻煩

# 鎖表

select .... for update;

待研究

# redis

網路爬到有用 redis 解決，有時間在理解
[使用 Redis SETNX 命令实现分布式锁 - Leo 的博客 - CSDN 博客](https://blog.csdn.net/lihao21/article/details/49104695)

[高并发场景下数据重复插入的问题 - LEON 的博客 - CSDN 博客](https://blog.csdn.net/qq_28018283/article/details/80241090)
[如何去掉数据库重复记录并且只保留一条记录 - Java 高知 - CSDN 博客](https://blog.csdn.net/tjcyjd/article/details/8950621)
[高并发时，进行 insert 操作出现重复记录问题分析 - hansam 的专栏 - CSDN 博客](https://blog.csdn.net/u012721013/article/details/64451644)
[java 解决分布式环境中 高并发环境下数据插入重复问题 - 天才白痴梦 - CSDN 博客](https://blog.csdn.net/qq_22956867/article/details/72529144)
[并发写数据库如何避免重复数据-CSDN 论坛](https://bbs.csdn.net/topics/391861174)
[如何用DB抗住大量读写的业务 – fishermartyn.github.io](https://fishermartyn.github.io/blog/High-traffic-DB-system/)
[处理高并发情况下的DB插入 - isWTF's blog](https://blog.iswtf.com/article.php?id=1353)