---
title: MySQL不錯優化文章&DB雜記
date: 2018-12-27 23:09:16
tags: [mysql]
categories: MySQL
---

小記網路看到的好文章

<!--more-->

## Mysql 好文

[我必须得告诉大家的 MySQL 优化原理 - 简书](https://www.jianshu.com/p/d7665192aaaf) {% asset_link 1.png 備份圖 %}
[我必须得告诉大家的 MySQL 优化原理 2 - 简书](https://www.jianshu.com/p/01b9f028d9c7) {% asset_link 2.png 備份圖 %}
[我必须得告诉你的 MySQL 优化原理 3 - 简书](https://www.jianshu.com/p/78b6b7e2bb7f) {% asset_link 3.png 備份圖 %}
[jobbole/awesome-mysql-cn: MySQL 资源大全中文版，分析工具、备份、性能测试、配置、部署、GUI 等](https://github.com/jobbole/awesome-mysql-cn)

## Sharding

[MySql 從一竅不通到入門（五）Sharding：分表、分庫、分片和分割槽 - IT 閱讀](https://www.itread01.com/content/1545726433.html)
[MySQL Sharding 详解 - 简书](https://www.jianshu.com/p/6ebafabfddae)
[MongoDB Sharding 分散式儲存架構建置 (概念篇) - Soul & Shell Blog](https://blog.toright.com/posts/4552/mongodb-sharding-%E5%88%86%E6%95%A3%E5%BC%8F%E5%84%B2%E5%AD%98%E6%9E%B6%E6%A7%8B%E5%BB%BA%E7%BD%AE-%E6%A6%82%E5%BF%B5%E7%AF%87.html/amp)

### mariadb sipder(mysql sharding)

- [Spider 引擎分布式数据库解决方案（最全的 spider 教程） - 简书](https://www.jianshu.com/p/b96a8c90689a) {% asset_link 4.png 備份圖 %}
- [aipescience/spider-docker: Docker files for a Spider-Engine MariaDB cluster](https://github.com/aipescience/spider-docker)
- [MySQL 原生的分库分表方案 - Spider 存储引擎 - 知乎](https://zhuanlan.zhihu.com/p/47418626)
- [MySQL 存储引擎之 Spider 内核深度解析-云栖社区-阿里云](https://yq.aliyun.com/articles/80458)
- [MySQL 中间件 Spider 引擎问题集锦 - 简书](https://www.jianshu.com/p/efb31df1119c)

這邊我有自已用[aipescience/spider-docker: Docker files for a Spider-Engine MariaDB cluster](https://github.com/aipescience/spider-docker)
做一個簡單環境
再搭配了[Spider 引擎分布式数据库解决方案（最全的 spider 教程） - 简书](https://www.jianshu.com/p/b96a8c90689a)教學做出一個 Sipder 環境
使用結論：在使用 Sipder 做 alter table 時候，後面的 DB 不會跟著改變
必須各別調整，在管理上面感覺不會非常方便
不知道是不是有很好用的部屬工具可以解決?

[Cluster 和 HA 的差別](https://millenniummeetonce.blogspot.com/2015/12/clusterha.html)
[Server NLB 和 Cluster 架構 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10087065)

## binlog

- [初探 MySQL 的 Binlog · 一个伪宅级别的码畜。](https://xcoder.in/2015/08/10/mysql-binlog-try/)
- [MySQL 使用 binary log 回復 某段時間區間 的資料 | Tsung's Blog](https://blog.longwin.com.tw/2009/04/mysql-binlog-recovery-range-by-date-time-2009/)
- [腾讯工程师带你深入解析 MySQL binlog - 知乎](https://zhuanlan.zhihu.com/p/33504555)
- [adalf 的小技巧: [MySQL]查看 Binlog 的內容](http://adalf0722.blogspot.com/2014/11/mysqlbinlog.html)

## RabbitMQ kafka

[Kafka、RabbitMQ、RocketMQ 之间的区别是什么 ? - 知乎](https://www.zhihu.com/question/275090117)
[消息中间件部署及比较：rabbitMQ、activeMQ、zeroMQ、rocketMQ、Kafka、redis - 掘金](https://juejin.im/post/5b32044ef265da59654c3027)

### kafka

- [使用 Docker 快速搭建 Kafka 开发环境 | Tomoya's Blog](https://tomoyadeng.github.io/blog/2018/06/02/kafka-cluster-in-docker/)
- [Kafka 深度解析 | 技术世界 | kafka,大数据,集群,kafka 架构,技术世界,郭俊 Jason,大数据架构,Kafka HA,kafka benchmark,kafka split brain,kafka 脑裂](http://www.jasongj.com/2015/01/02/Kafka%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90/index.html)
- [在 Docker 上运行 Apache Kafka - DockOne.io](http://dockone.io/article/565)
- [[Day06] Kaggle 的解題挑戰 - Kafka 實作 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10192284)
