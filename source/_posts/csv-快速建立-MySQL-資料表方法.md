---
title: csv 快速建立 MySQL 資料表方法
date: 2019-08-21 20:40:12
tags: [mysql]
categories: MySQL
---

通常 csv 匯入現有 table
我今天要建立 table
~~但我很懶(逃)~~
在 stackoverflow 找到非常簡單的方法

<!--more-->


[database - Create mysql table directly from CSV file using the CSV Storage engine? - Stack Overflow](https://stackoverflow.com/questions/9998596/create-mysql-table-directly-from-csv-file-using-the-csv-storage-engine)
```sh
#!/bin/sh
# pass in the file name as an argument: ./mktable filename.csv
echo "create table $1 ( "
head -1 $1 | sed -e 's/,/ varchar(255),\n/g'
echo " varchar(255) );"
```

只要在 command line，參數打 csv 檔案名稱
`sh xxx.sh xxx.csv`
可以產生出 create table SQL



## 匯入 CSV
由於我是用 Dbeaver 匯入(不知道 phpmyadmin不讓我匯入)
有空再來補 SQL 要怎麼做
~~結束這回合~~
我遇到資料有換行的問題 Dbeaver 可以正常執行
