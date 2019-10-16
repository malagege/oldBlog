---
title: MySQL 比較兩個資料庫方法
date: 2019-09-12 20:24:25
tags: [mysql]
categories: MySQL
---

先留者，有空再實作看看

> Red-Gate's MySQL Schema & Data Compare (Commercial)
> Maatkit (now Percona)
> liquibase
> Toad
> Nob Hill Database Compare (Commercial)
> MySQL Diff
> SQL EDT (Commercial)

[Compare two MySQL databases - Stack Overflow](https://stackoverflow.com/questions/225772/compare-two-mysql-databases)
[檔案收發系統 - IT閱讀](https://www.itread01.com/p/444490.html)

**2019-10-07**
聽說可以 table 比對 table 產生 sql 語法
查了一下還真的有!!
[(1) How to Compare two Databases using MySQL Workbench - YouTube](https://www.youtube.com/watch?v=YzEuCa5Bca0)
[(1) mysql compare databases and sync | Mysql tutorial - YouTube](https://www.youtube.com/watch?v=_mpph3mLEt4)
[mmatuson/SchemaSync: A MySQL Schema Versioning and Migration Utility](https://github.com/mmatuson/SchemaSync)
[[Mysql] 使用mysqldiff和mysqldbcompare检查数据一致性 | zeven's blog](https://zeven0707.github.io/2018/10/08/%E4%BD%BF%E7%94%A8mysqldiff%E5%92%8Cmysqldbcompare%E6%AF%94%E8%BE%83%E6%95%B0%E6%8D%AE%E5%BA%93%E5%B7%AE%E5%BC%82/)

```
$ mysqldbcompare --server1=root:root@localhost --server2=root:root@localhost db1:db2 --changes-for=server1 -a --difftype=sql


# WARNING: Objects in server1.db1 but not in server1.db2:
# TABLE: table2
#
# WARNING: Objects in server1.db2 but not in server1.tb1:
# TABLE: table3
#
#                                                   Defn    Row     Data
# Type      Object Name                             Diff    Count   Check
#-------------------------------------------------------------------------
# TABLE     t1                                      pass    pass    -
#           - Compare table checksum                                FAIL
#           - Find row differences                                  FAIL
#
# Transformation for --changes-for=server1:
#

# Data differences found among rows:
UPDATE db1.t1 SET b = 'Test 123' WHERE a = '1';
UPDATE db1.t1 SET b = 'Test 789' WHERE a = '3';
DELETE FROM db1.t1 WHERE a = '4';
INSERT INTO db1.t1 (a, b) VALUES('5', 'New row - db2');


# Database consistency check failed.
#
# ...done
```
[mysqldbcompare MySQL数据库比较工具 - 歪麦博客](https://www.awaimai.com/1531.html)
