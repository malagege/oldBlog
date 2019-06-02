---
title: MySQL(MariaDB) insert有關做相同表會遇到問題小記
date: 2019-06-02 00:39:51
tags: [mysql]
categories: MySQL
---

最近做 INSERT 很簡單語法
但竟然出錯
印象中 DB2 以前這樣下沒有這個問題
錯誤訊息如下：
`Table 'test' is specified twice, both as a target for 'INSERT' and as a separate source for data`
`You can't specify target table 'basic_data' for update in FROM clause`
ex:

```sql
INSERT INTO data_waktu_vaksinasi (id_binatang, id_vaksin, tanggal_vaksin, status_vaksin) VALUES
    (1, 1, (SELECT DATE_ADD(max(tanggal_vaksin), INTERVAL 1 YEAR)) FROM data_waktu_vaksinasi, 'belum')
```

<!--more-->

爬了一些網站有找到解法
[解决 mysql 不能修改（You can't specify target table 'basic_data' for update in FROM clause） - winter13292 的专栏 - CSDN 博客](https://blog.csdn.net/winter13292/article/details/8628953)
[mysql - table is specified twice both as a target for INSERT and as separate source of data - Stack Overflow](https://stackoverflow.com/questions/34839842/table-is-specified-twice-both-as-a-target-for-insert-and-as-separate-source-of-d)
[1093 Error in MySQL table is specified twice - Stack Overflow](https://stackoverflow.com/questions/47165521/1093-error-in-mysql-table-is-specified-twice?rq=1)

```sql
INSERT INTO data_waktu_vaksinasi (id_binatang, id_vaksin, tanggal_vaksin, status_vaksin)
     SELECT 1, 1, DATE_ADD(max(tanggal_vaksin), INTERVAL 1 YEAR), 'belum'
     FROM data_waktu_vaksinasi;
```

或外面包一個 `SELECT`
[解决 mysql 不能修改（You can't specify target table 'basic_data' for update in FROM clause） - winter13292 的专栏 - CSDN 博客](https://blog.csdn.net/winter13292/article/details/8628953)

也有看到 join 方式解決
[mysql: you can't specify target table 问题解决 - weixin_42608550 的博客 - CSDN 博客](https://blog.csdn.net/weixin_42608550/article/details/89644418)

其他小記
[如何去掉数据库重复记录并且只保留一条记录 - Java 高知 - CSDN 博客](https://blog.csdn.net/tjcyjd/article/details/8950621)
