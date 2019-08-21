---
title: MySQL 查看資料表權限
date: 2019-08-14 22:06:21
tags: [mysql]
categories: MySQL
---

最近想看資料表有哪些 user 權限
因為不常下，所以特別筆記

<!--more-->

[permissions - mysql: Show GRANTs for all users - Database Administrators Stack Exchange](https://dba.stackexchange.com/questions/23265/mysql-show-grants-for-all-users)

裡面有很多方法，後來挑選出這個，可搭配 where 查詢

```sql
select Host, Db, User, Insert_priv, Update_priv, Delete_priv, Create_tmp_table_priv, Alter_priv from mysql.db
```

[查詢 MySQL 對 此帳號 開放(GRANT)哪些權限 | Tsung's Blog](https://blog.longwin.com.tw/2009/06/query-mysql-show-grant-permission-2009/)

```sql
SHOW GRANTS;
SHOW GRANTS FOR CURRENT_USER;
SHOW GRANTS FOR CURRENT_USER();
```
