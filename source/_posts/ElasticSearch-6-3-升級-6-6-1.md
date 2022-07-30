---
title: ElasticSearch 6.3 升級 6.6.1
date: 2022-07-30 23:58:26
tags: [elasticsearch]
categories: ElasticSearch
---

最近因為 ES 安全性問題，手上接的專案 ES 需要升級 6.6.1，這邊我沒有相關經驗，簡單整理一下筆記。

<!--more-->

## 流程

### 備份 es 資料夾


###  覆蓋對應 ES 資料夾檔案
ES 6.6.1 部分資料夾覆蓋到
1. bin
2. lib
3. modules


### Plugin 升級檔案到相對應路徑

plugin 查看有哪些東西，去官網做升級下載檔案動作。

elasticsearch-sql 套件
https://github.com/NLPchina/elasticsearch-sql/releases/tag/6.6.1.0

可以把 `plugins\sql` 做覆蓋。


官方安裝套件是用 command line 執行，可以參考看看。安裝:[Installation Guide · NLPchina/elasticsearch-sql Wiki · GitHub](https://github.com/NLPchina/elasticsearch-sql/wiki/Installation-Guide)
```java=
./bin/elasticsearch-plugin install file:///home/yourFolder/elasticsearch-sql-x.x.x.x.zip
```



![](https://i.imgur.com/HpaSQL0.png)

