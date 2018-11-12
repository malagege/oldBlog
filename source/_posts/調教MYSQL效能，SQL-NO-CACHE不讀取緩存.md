---
title: 調教MYSQL效能，SQL_NO_CACHE不讀取緩存
date: 2018-05-17 20:23:20
tags: [MySQL,sql]
categories: MySQL
---

最近在專案使用phpmyadmin測試sql的時候
發現第一次執行需要3秒的時間
第二次是0.3秒的時間
但是...我不可能一直的等很久再做Query動作
後來有在網路上看到在 *select* 後面插入SQL_NO_CACHE
可以解決這個問題


* [Mysql 简单SELECT，第一次很慢，以后很快，这是什么问题 - 第2页 - MySQL及其它开源数据库 - ITPUB论坛－中国最专业的IT技术社区](http://www.itpub.net/thread-1755321-2-1.html)
* [你真的會用mysql 的SQL_NO_CACHE嗎？ - 每日頭條](https://kknews.cc/zh-tw/other/aenlgex.html)