---
title: 快速調整 INERT / UPDATE SQL 語法
date: 2019-12-19 20:37:36
tags: [sql]
categories: SQL
---

最近公司兩台 DB 資料不一致
我可以直接使用 Dbeaver 生產 SQL
但我發現相關欄位另外一台 DB 沒有
移除爛為在 Editor 欄位和數值非常傷眼睛又很花時間

<!--more-->

後來發現 Dbeaver 能解決問題

## Dbeaver 生產 SQL

[SQL Generation · dbeaver/dbeaver Wiki](https://github.com/dbeaver/dbeaver/wiki/SQL-Generation)

在查詢可以用以上連結方法生產
不過，如何移除不需要的欄位呢?
其實我發現用[Data Filters · dbeaver/dbeaver Wiki](https://github.com/dbeaver/dbeaver/wiki/Data-Filters)
可以移除你不想要的欄位
生產 INSERT / UPDATE 語法
UPDATE 建議帶入 PK
這樣產生 SQL 比較沒問題

## 手動自己建立 DB

首先需要自己 create table(通常會用匯出)
INSERT INTO 資料再作刪除欄位
匯出 SQL

目前覺得第一個方法比較實用
有想到更好的方法再回來補

拿到 CSV 資料可以用 [csv 快速建立 MySQL 資料表方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/08/21/csv-%E5%BF%AB%E9%80%9F%E5%BB%BA%E7%AB%8B-MySQL-%E8%B3%87%E6%96%99%E8%A1%A8%E6%96%B9%E6%B3%95/)