---
title: 快速產生資料表 alter table 語法 schemalex
date: 2020-01-02 22:26:32
tags: [sql]
categories: 實用工具
---

常常我們在測試 DB 環境
不會馬上上到正式環境
但都是分開改 alter table
之前有在 Dbeaver 有 migrate table 功能
但大致上我們工程師沒有 DB 的權限
要如何快速產生 alter table 語法呢?

<!--more-->

其實之前有寫過 [MySQL 比較兩個資料庫方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/09/12/MySQL-%E6%AF%94%E8%BC%83%E5%85%A9%E5%80%8B%E8%B3%87%E6%96%99%E5%BA%AB%E6%96%B9%E6%B3%95/)
但一樣沒有產生 alter table sql 語法...
裡面的範例也沒有使用過
今天我發現 github 裡面有一個 schemalex
可以輕鬆產生 alter table 語法

## schemalex

[schemalex/schemalex: Generate difference sql of two mysql schema](https://github.com/schemalex/schemalex)

```sql
-- old.sql
CREATE TABLE hoge (
    id INTEGER NOT NULL AUTO_INCREMENT,
    PRIMARY KEY (id)
);
```


```sql
-- new.sql
CREATE TABLE hoge (
    id INTEGER NOT NULL AUTO_INCREMENT,
    c VARCHAR (20) NOT NULL DEFAULT "hoge",
    PRIMARY KEY (id)
);

CREATE TABLE fuga (
    id INTEGER NOT NULL AUTO_INCREMENT,
    PRIMARY KEY (id)
);
```

schemalex old.sql new.sql

就會印出 Alter table SQL

好處只要用phpmyadmin 匯出 create table 就可以完成!!
更多用法可以參考官網文件[schemalex/schemalex: Generate difference sql of two mysql schema](https://github.com/schemalex/schemalex)

## 快速產生 create sql 語法

```
SHOW CREATE TABLE tbl_name
```

相關查詢資料:
[MySQL :: MySQL 8.0 Reference Manual :: 13.7.7.10 SHOW CREATE TABLE Statement](https://dev.mysql.com/doc/refman/8.0/en/show-create-table.html)
[Go言語のデータベース用のライブラリを比較する - Koichi Ishida blog](https://wapa5pow.com/compare-golang-database-libraries/#k0kubunsqldef)
[schemalex/git-schemalex: database migration tool for mysql schema is managed via git](https://github.com/schemalex/git-schemalex)
[k0kubun/sqldef: Idempotent MySQL/PostgreSQL schema management by SQL](https://github.com/k0kubun/sqldef)
[rubenv/sql-migrate: SQL schema migration tool for Go.](https://github.com/rubenv/sql-migrate)
[如何快速比對資料庫結構差異並產生同步指令 - Yowko's Notes](https://blog.yowko.com/sql-server-schema-compare/)
[SchemaCrawler - Free database schema discovery and comprehension tool](https://www.schemacrawler.com/)