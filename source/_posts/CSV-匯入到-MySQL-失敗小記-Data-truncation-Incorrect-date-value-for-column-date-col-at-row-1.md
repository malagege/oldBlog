---
title: >-
  CSV 匯入到 MySQL 失敗小記(Data truncation: Incorrect date value: '' for column
  'date_col' at row 1)
date: 2019-09-09 22:33:55
tags: [mysql,csv]
categories: MySQL
---

最近自己產生出來的 csv 匯入 DB遇到一些問題
之前 [csv 快速建立 MySQL 資料表方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/08/21/csv-%E5%BF%AB%E9%80%9F%E5%BB%BA%E7%AB%8B-MySQL-%E8%B3%87%E6%96%99%E8%A1%A8%E6%96%B9%E6%B3%95/) 真的建立資料表很方便
但最近採到一些雷
在這邊整理一下

<!--more-->

## 用腳本 create table 失敗

[csv 快速建立 MySQL 資料表方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/08/21/csv-%E5%BF%AB%E9%80%9F%E5%BB%BA%E7%AB%8B-MySQL-%E8%B3%87%E6%96%99%E8%A1%A8%E6%96%B9%E6%B3%95/)
用裡面腳本`create table`竟然失敗
```
Row size too large. The maximum row size for the used table type, not counting BLOBs, is 65535. This includes storage overhead, check the manual. You have to change some columns to TEXT or BLOBs
```

簡單說一下，就是一個varchar 是有限制大小，可能轉成`text`或著去原本 SQL 把他 `create table` 匯出來就可以了
相關連結:
- [10.4 限制表的列數和行的大小 - IT閱讀](https://www.itread01.com/articles/1476376830.html)
- [10.4 限制表的列数和行的大小 - 程序园](http://www.voidcn.com/article/p-eeaxkihv-bhh.html)


## Data truncation: Incorrect date value: '' for column  'date_col' at row 1

這個問題困擾我很久了
我後來發現是 `NULL` 在 csv 跟空字串一樣
(這卻說csv `,,` 跟 `,"",` 都是一樣的...)
好像 csv 跟 txt 沒法表示 NULL
不過有看到官網`\n`可以代表是 NULL [MySQL :: MySQL 8.0 Reference Manual :: 9.1.7 NULL Values](https://dev.mysql.com/doc/refman/8.0/en/null-values.html)
但是我是用別的程式匯出 csv 
dbeaver 嘗試著匯出，再用 dbeaver 匯入
結果一樣會有這個問題....(沒有看到\n)
這還是未解之謎
但不知道是不是用 dbeaver 跟用 `SELECT ... INTO OUTFILE` 原理不一樣

[MySQL :: MySQL 8.0 Reference Manual :: 13.2.7 LOAD DATA Syntax](https://dev.mysql.com/doc/refman/8.0/en/load-data.html)

### 解決方法


#### 標註 NULL 特殊字串

[CSV import NULL string mark support · Issue #4223 · dbeaver/dbeaver](https://github.com/dbeaver/dbeaver/issues/4223)
[MySQL Export Table to CSV](http://www.mysqltutorial.org/mysql-export-table-to-csv/)

標註 NULL 欄位很簡單`ifnull(my_column, "")`

**DBeaver 匯入可以選擇 null 取代**

![](https://user-images.githubusercontent.com/12808832/35556162-7c08d688-05dc-11e8-9d97-2cbd451f3648.png)

#### STR_TO_DATE

[How to import a CSV file into a MySQL database? - Miguel Gomez - Medium](https://medium.com/@AviGoom/how-to-import-a-csv-file-into-a-mysql-database-ef8860878a68)

`SELECT STR_TO_DATE(date_col), "%M %d %Y");`

STR_TO_DATE("", "%M %d %Y")
> 0000-00-00
STR_TO_DATE(NULL, "%M %d %Y")
> NULL
目前猜測
```
load data local infile 'file.txt' into table tableName 
SET payDate = str_to_date(@payDate, '%d/%m/%Y');
```
@payDate 應該是 '' 空字串

#### IF 大法

[MySQL load NULL values from CSV data - Stack Overflow](https://stackoverflow.com/questions/2675323/mysql-load-null-values-from-csv-data)

```sql
LOAD DATA infile '/tmp/testdata.txt'
INTO TABLE moo
fields terminated BY ","
lines terminated BY "\n"
(@vone, @vtwo, @vthree, @vfour, @vfive)
SET
one = nullif(@vone,''),
two = nullif(@vtwo,''),
three = nullif(@vthree,''),
four = nullif(@vfour,'')
;
```

與上方法大同小異，但我覺得上一種方法會比較好處理 NULL 放到 date欄位

#### ,, 取代法(不要這樣用)

這個方法 ,, 會取代掉空字串
所以不要這樣用 QQ


感覺還是沒有好用的方法
匯出直接轉 sql 出來還是最方便的

[mysql null problem? - Stack Overflow](https://stackoverflow.com/questions/4588364/mysql-null-problem)


#### PHP 執行方法

剛剛爬到有做 null 處理

[sql - MySQL - SELECT * INTO OUTFILE LOCAL ? - Stack Overflow](https://stackoverflow.com/questions/2867607/mysql-select-into-outfile-local)

```bash
wget https://gist.githubusercontent.com/paslandau/37bf787eab1b84fc7ae679d1823cf401/raw/29a48bb0a43f6750858e1ddec054d3552f3cbc45/mysql2csv -O mysql2csv -q && (sha256sum mysql2csv | cmp <(echo "b109535b29733bd596ecc8608e008732e617e97906f119c66dd7cf6ab2865a65  mysql2csv") || (echo "ERROR comparing hash, Found:" ;sha256sum mysql2csv) ) && chmod +x mysql2csv
```


發現新的 sqltocsv 工具
[StabbyCutyou/sqltocsv: Stream large SQL result sets into a local file](https://github.com/StabbyCutyou/sqltocsv)
但不知道有沒有對 null 做處理

[karissa/sql2csv: Commandline utility for easily converting a SQL select statement into csv](https://github.com/karissa/sql2csv)