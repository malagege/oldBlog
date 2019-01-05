---
layout: post
title: "SQL子查詢筆記(相關子查詢/非相關子查詢)"
date: 2017-12-23 17:15
comments: true
categories: SQL
tags: [sql, 相關子查詢, 非相關子查詢]
---

~~之前用子查詢第二層，竟然抓不到外層的欄位~~
已經找到 [終於知道為什麼子查詢沒辦法抓取外面的表 | 程式狂想筆記](https://malagege.github.io/blog/2018/12/24/%E7%B5%82%E6%96%BC%E7%9F%A5%E9%81%93%E7%82%BA%E4%BB%80%E9%BA%BC%E5%AD%90%E6%9F%A5%E8%A9%A2%E6%B2%92%E8%BE%A6%E6%B3%95%E6%8A%93%E5%8F%96%E5%A4%96%E9%9D%A2%E7%9A%84%E8%A1%A8/)
有時候跟本不知道為什麼?後來問公司的前軰，他說第二層就抓不到最外一層
但這種強制記這種觀念感覺不是很好，今天 Google 查一下原因
在這邊整理一下 XD

<!--more-->

今天[多層次子查詢的 sql 執行順序的問題-wj_zizi-ITPUB 博客](http://blog.itpub.net/16591471/viewspace-695491/)和[你真的会玩 SQL 吗？无处不在的子查询 - 文章 - 伯乐在线](http://blog.jobbole.com/95027/)
看到子查詢有分兩類

1. 相關子查詢
2. 非相關子查詢

這兩個其實不難分，不要被兩個專有名詞嚇到
先講非相關子查詢，簡單講就是子查詢跟外面 SQL 查詢內容沒有相關
非相關子查詢只會跑一次，所以效能上會非常快
執行順序也比外層先，所以這也是為什麼**非相關子查詢**沒法取得外層欄位的原因
範例如下，以下子查詢只會查一次

```sql
SELECT *
FROM xxxx
WHERE class = (SELECT max(cost)
FROM bbbb
)
```

相關子查詢就反知，比較吃效能，每一行都會執行 loop
先不談[slides/SQL_reporting.pdf at master · TritonHo/slides](https://github.com/TritonHo/slides/blob/master/Taipei%202016-08%20talk/SQL_reporting.pdf)裡面有介紹有些 DB 會自動做 join 動作
這個我們比較常用到

```sql
SELECT＊
FROM xxxx a
WHERE cost > (SELECT avg(cost)
FROM bbbb
where class = a.class
)
```

```sql
SELECT *
  FROM a1
WHERE EXISTS
(SELECT 'X'
          FROM (SELECT w2.corp_id FROM a1 w2 WHERE w2.period_id <= a1.period_id) u
         WHERE u.corp_id = a1.corp_id);

```

[第二层子查询居然不认最外层表中的列 - Oracle 开发 - ITPUB 论坛－中国最专业的 IT 技术社区](http://www.itpub.net/thread-1027910-1-1.html#post_11069036)看到有不能跑的 example
目前猜測 form 是非相關子查詢，我猜因為 from 會先執行，所以抓不到外層

[sql 语法相关子查询与非相关子查询-流浪的野狼-ITPUB 博客](http://blog.itpub.net/28612416/viewspace-773763/)看到一個標量子查詢(相對於多值子查詢)名稱

```sql
select OrderId From Orders where EmployeeId IN
(select EmployeeId From employees where lastName like 'Da%')
```

> 將'Davolio'改為'D%'時,這個時候子查詢中返回結果為 2 行,等號右邊此時為多值,查詢失敗.將'='改為 in 謂詞.查詢才能通過.
> employees 表中無 lastname='jason',外部查詢將返回 null.

## 結論

SQL 有太多東西要學了 XD
這些離清楚之後比較不容易採到雷
要學的東西太多了 orz

###20171226 無聊寫的 SQL

```sql
SELECT a.id, a.rev, a.content
FROM `docs` a
WHERE a.id  IN  (
    SELECT max(id)
    FROM `docs` b
    WHERE  b.id IN (SELECT id
                    FROM `docs` c
                    WHERE c.id=a.id)
) ;
```

http://sqlfiddle.com/#!9/a6c585/30596
原本以為只會有一筆

是 From a table 先執行 而 a 有四筆
子查詢關連 a.id
所以結果是 4 筆沒錯
做 max 也會沒有意義

# 參考來源

[你真的会玩 SQL 吗？无处不在的子查询 - 文章 - 伯乐在线](http://blog.jobbole.com/95027/)

[对象-关系数据库管理系统原理与实现 - 李战怀, 李红燕, 徐秋元 - Google 圖書](https://books.google.com.tw/books?id=OhdwHgymgtcC&pg=PA146&lpg=PA146&dq=%E9%9D%9E%E7%9B%B8%E9%97%9C%E5%AD%90%E6%9F%A5%E8%A9%A2&source=bl&ots=0LYsR2GAVw&sig=pLLTLhWDAIn-Pi1bBbW96wvTkFg&hl=zh-TW&sa=X&ved=0ahUKEwj-3eyKzKDYAhUKT7wKHbkyAKQ4ChDoAQg1MAI#v=onepage&q=%E9%9D%9E%E7%9B%B8%E9%97%9C%E5%AD%90%E6%9F%A5%E8%A9%A2&f=false)

[你真的会玩 SQL 吗？无处不在的子查询 - 文章 - 伯乐在线](http://blog.jobbole.com/95027/)

[多層次子查詢的 sql 執行順序的問題-wj_zizi-ITPUB 博客](http://blog.itpub.net/16591471/viewspace-695491/)

[第二层子查询居然不认最外层表中的列 - Oracle 开发 - ITPUB 论坛－中国最专业的 IT 技术社区](http://www.itpub.net/thread-1027910-1-1.html#post_11069036)

[\[SQL \] 兩層以上子查詢辨認 Table 別名 - 看板 Database - 批踢踢實業坊](https://www.ptt.cc/bbs/Database/M.1152841888.A.596.html)

[相關子查詢和嵌套子查詢 - 問問題](http://www.wenwenti.info/article/72559)

[MySql 學習(三) —— 子查詢(where、from、exists) 及 連線查詢(left join、right join、inner join、union join) - 掃文資訊](https://tw.saowen.com/a/a4610b9e7cb588e070f8e48aade920ab12a63ef681d994ccbfcb6753553bc466)

[MySql 学习(三) —— 子查询(where、from、exists) 及 连接查询(left join、right join、inner join、union join) - bojiangzhou - 博客园](http://www.cnblogs.com/chiangchou/p/mysql-3.html)

[sql 语法相关子查询与非相关子查询-流浪的野狼-ITPUB 博客](http://blog.itpub.net/28612416/viewspace-773763/)
