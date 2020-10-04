---
title: Oracle 匯出 Schema 好用工具
date: 2020-09-03 20:47:56
tags: [oracle,db2]
categories: Oracle
---

最近公司專案需要建制某環境 Table Schema
但是現有工具沒辦法做到大量匯出Table
後來 Google 好用的工具

<!--more-->

## scheme2ddl 

[qwazer/scheme2ddl: Command line util for export oracle schema to set of ddl scripts](https://github.com/qwazer/scheme2ddl)

java -jar scheme2ddl-2.4.3-SNAPSHOT.jar -url _DB_USERID_/_DB_PASSWORD_:@_Oracle_IP_:1521:_DBNAME_ -o D:/temp/

執行速度非常快!!


## 其他看到工具

MySQL 就沒找了，現成 phpmyadmin 都可以做到

不過，剛剛有發現看起來好用工具[k0kubun/sqldef: Idempotent MySQL/PostgreSQL schema management by SQL](https://github.com/k0kubun/sqldef)
之前有紀錄這些工具[快速產生資料表 alter table 語法 schemalex | 程式狂想筆記](https://malagege.github.io/blog/2020/01/02/%E5%BF%AB%E9%80%9F%E7%94%A2%E7%94%9F%E8%B3%87%E6%96%99%E8%A1%A8-alter-table-%E8%AA%9E%E6%B3%95-schemalex/)

DB2 剛好有看到[qwazer/db2-scheme2ddl: Command line util for export IBM DB2 schema to set of ddl scripts](https://github.com/qwazer/db2-scheme2ddl)
不過我就沒測試了

[qwazer/db2-table2sql](https://github.com/qwazer/db2-table2sql)
