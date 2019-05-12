---
title: MySQL delete 一次刪除指定多個資料表欄位方法
date: 2019-04-20 23:53:04
tags: [mysql, delete]
categories: MySQL
---

上一篇，要嘗試刪除語句
在想刪除主表後，其餘關聯表示不是需要手動刪除??
但這篇先不談這個
最近看到兩篇發現 sql delete 可以做 join 動作刪除!!!
先來筆記吧!!

<!--more-->

- [SQL：如何实现带有内连接的 DELETE？ - 问答 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/ask/55599)
- [SQL 中使用 update inner join 和 delete inner join - linFen - 博客园](https://www.cnblogs.com/luluping/archive/2009/05/21/1486549.html)

發現可能不是每個 DB 都可以這樣做
db2 可能沒有!! [IBM DELETE 介紹頁面](https://www.ibm.com/support/knowledgecenter/en/SSEPEK_10.0.0/sqlref/src/tpc/db2z_sql_delete.html)
但為什麼我想一次做完呢?
因為現在 DB 我們公司做 auto commit
所以我想一次解決 XDD

指細看也沒有人說不要用

```sql
-- DELETE 刪除大法
delete a1, a2 FROM `db`.`a1_table` a1 JOIN `db`.`a2_table` a2 ON a1.id = a2.a1_id WHERE a1.id = 1;
-- 另類刪除方法
delete a1, a2 FROM `db`.`a1_table` a1, `db`.`a2_table` a2 WHERE a1.id = a2.a1_id and a1.id =1;
```

這邊有小提示

delete `...` from 中間是指要刪除的表格(可以使用自訂的 table)

這邊上面用 inner join 會遇到有一個問題
所以當關聯表沒資料，就不會刪掉
所以改成 left join 會比較好

強者我同事之前提過 letcode 有人這樣解題
[leetcode/database/DeleteDuplicateEmails at master · int32bit/leetcode](https://github.com/int32bit/leetcode/tree/master/database/DeleteDuplicateEmails)

```sql
DELETE p1 FROM Person p1,
    Person p2
WHERE
    p1.Email = p2.Email AND p1.Id > p2.Id
```

[MySQL DELETE JOIN: Deleting Data from Multiple Tables](http://www.mysqltutorial.org/mysql-delete-join/)
先記到這樣!!!
