---
title: 在MySQL5.0查詢大量資料使用IN子查詢好慢(優化方法)
date: 2018-05-15 20:01:47
tags: [MySQL, IN, 效能]
categories: SQL
---

最近使用有一個程式SQL會跑3秒
程式共花了1x秒鐘
想把他cost 時間降下來
但IN方法不知道為什麼那麼慢
就簡單研究有沒有更好的寫法

<!--more-->

```sql
SELECT  ptm.tablename
FROM `secord_big_table` ptm
WHERE ptm.playlistID  IN
    (SELECT stm.playlistID
     FROM `first_big_table` stm 
     WHERE stm.sid IN
         (SELECT sid
          FROM `small_table`
          WHERE as_id = 1
            AND item_no = 1))
```

執行`explain`看一下狀態...
first_big_table是掃全表的樣子

暫時用LIMIT和=解決問題
```sql
SELECT  ptm.tablename
FROM `secord_big_table` ptm
WHERE ptm.playlistID  =
   (SELECT stm.playlistID
     FROM `first_big_table` stm 
     WHERE stm.sid IN
         (SELECT sid
          FROM `small_table`
          WHERE as_id = 1
            AND item_no = 4)
         LIMIT 0,1
         )
```

**事後發現**
這不是我之前寫的`非相關子查詢`嗎
[SQL子查詢筆記(相關子查詢/非相關子查詢) « 進擊的程式新手](http://malagege.logdown.com/posts/4690995-sql-subquery-notes)
照到理沒理由這麼慢阿...
好啦，這時候突然回想起來[Backend 台灣 (Backend Tw)](https://www.facebook.com/groups/616369245163622)裡面的T大常常批評MySQL
~~在絕望下~~尋找新方法


## exists優化IN語句

> 第一種格式是使用IN操作符：
... where column in(select * from ... where ...);
第二種格式是使用EXIST操作符：
... where exists (select 'X' from ...where ...);
我相信絕大多數人會使用第一種格式，因為它比較容易編寫，而實際上第二種格式要遠比第一種格式的效率高。在Oracle中可以幾乎將所有的IN操作符子查詢改寫為使用EXISTS的子查詢。
第二種格式中，子查詢以‘select 'X'開始。運用EXISTS子句不管子查詢從表中抽取什麼資料它只查看where子句。這樣優化器就不必遍曆整個表而僅根據索引就可完成工作（這裏假定在where語句中使用的列存在索引）。相對於IN子句來說，EXISTS使用相連子查詢，構造起來要比IN子查詢困難一些。
通過使用EXIST，Oracle系統會首先檢查主查詢，然後運行子查詢直到它找到第一個匹配項，這就節省了時間。Oracle系統在執行IN子查詢時，首先執行子查詢，並將獲得的結果列表存放在在一個加了索引的臨時表中。在執行子查詢之前，系統先將主查詢掛起，待子查詢執行完畢，存放在臨時表中以後再執行主查詢。這也就是使用EXISTS比使用IN通常查詢速度快的原因
[[效能]SQL語句性能調整原則 @ Programmer Developer Notebook :: 隨意窩 Xuite日誌](http://blog.xuite.net/javax/programmer/3505144-%5B%E6%95%88%E8%83%BD%5DSQL%E8%AA%9E%E5%8F%A5%E6%80%A7%E8%83%BD%E8%AA%BF%E6%95%B4%E5%8E%9F%E5%89%87+)

## 實戰MySQL5.0上面
有在[IN和EXISTS的差異 @ 芊芊的窩 :: 痞客邦 ::](http://eeluck.pixnet.net/blog/post/27559378-in%E5%92%8Cexists%E7%9A%84%E5%B7%AE%E7%95%B0)改成exists語句
>而假如外部的表格A是相對的儲存大量資料，則採用第一個方法IN的效率將會比較好，假如你使用EXISTS，則除了會對A這個大表格進行全面掃瞄外，還會一筆一筆讀取所有A的資料列，效能自然較差。
簡單的一句話，外大內小=IN，外小內大=EXISTS，這是一個實用的概略評估方法，在大部分的情況下是適用的。

我覺得優化原因是[slides/SQL_report_v2.pdf at master · TritonHo/slides](https://github.com/TritonHo/slides/blob/master/Taipei%202017-10%20talk/SQL_report_v2.pdf)的Query Optimizer淺談

不過我修改
```sql
SELECT stm.playlistID
     FROM `first_big_table` stm 
     WHERE exists
         (SELECT 'x'
          FROM `small_table`
          WHERE as_id = 1
            AND item_no = 1
            AND stm.sid IN sid)
```
還是沒用...

最後還是想到[Backend 台灣 (Backend Tw)](https://www.facebook.com/groups/616369245163622)裡面的T大常常批評MySQL


## 子查詢語句注意問題 

 [謝晒的PHP網頁設計: [MYSQL] 多層式子查詢 效能注意事項](http://seanphpbook.blogspot.tw/2015/08/mysql.html)





## 用程式組IN字串就PK

今天實驗結果，在MYSQL5.0使用子查詢IN改寫exists速度還是很慢
然後發現跟我之前寫的非相關子查詢速度有不一樣
但是改成IN('xxxx','oooo','fffff')
速度就比較快
所以目前先用程式拆兩段

```php
<?
        $array_in = array();
        foreach ($in_sid as  $value) {
            array_push($array_in,$value['sid']);
        }

 $sql = "select xxx from  ooo where oo IN (" .implode( "," , $array_in).")";
```

## 柳暗花明又一村

這樣跑子查詢竟然吃到key了.....
但不知道這樣跑好不好

```sql
SELECT b.sid,
  (SELECT ptm.tablename
   FROM `social`.`secord_big_table` ptm
   WHERE ptm.playlistID = b.a)
FROM
  (SELECT g.sid,

     (SELECT stm.playlistID
      FROM `social`.`first_big_table` stm
      WHERE stm.sid = g.sid) AS a
   FROM `pay`.`activity_guess_item` g
   WHERE as_id = 1
     AND item_no = 4) AS b
```

雖然程式只需要抓到一筆
但上面方法可以抓到多筆
效能能吃到key

算是另類解法 


## INNER JOIN解決方法

最後網路爬到用INNER JOIN 優化
[[慢查优化]慎用MySQL子查询，尤其是看到DEPENDENT SUBQUERY标记时 - 旁观者 - 博客园](http://www.cnblogs.com/zhengyun_ustc/p/slowquery3.html)
[MySQL性能优化小记：MySQL子查询很慢的问题 - Bash @ Linux - ITeye博客](http://codingstandards.iteye.com/blog/1344833)

```sql
SELECT stm.tablename
FROM 
    (SELECT ptm.playlistID
     FROM `secord_big_table` ptm
     INNER JOIN `smaill_table` g ON ptm.sid = g.sid
     WHERE as_id = 1
       AND item_no = 4 ) p
INNER JOIN
`first_big_table` stm
ON stm.playlistID  = p.playlistID
WHERE stm.playlistID  = p.playlistID

用這種解法，速度有變比較快
但這種寫法真的不時直覺...


## 結論

網路上查，好像MySQL到5.7和8.0效能上有很大的改進
希望我有機會能使用到...




參考來源:
* [MySQL特异功能之：Impossible WHERE noticed after reading const tables - 风轻扬的日志 - 网易博客](http://wangyuanzju.blog.163.com/blog/static/13029200691114035831/)
* [MySql Explain 的使用[转] - 不待人亲 - ITeye博客](http://budairenqin.iteye.com/blog/1754937)
* [MySQL EXPLAIN：Impossible WHERE noticed after re... - NILYANG](https://my.oschina.net/lxrm/blog/108309)
* [mysql - Why would an IN condition be slower than "=" in sql? - Stack Overflow](https://stackoverflow.com/questions/3417074/why-would-an-in-condition-be-slower-than-in-sql/3417190#3417190)
* [sql - This SELECT query takes 180 seconds to finish - Stack Overflow](https://stackoverflow.com/questions/3416076/this-select-query-takes-180-seconds-to-finish)
* [query optimization - MySQL "IN" queries terribly slow with subquery but fast with explicit values - Stack Overflow](https://stackoverflow.com/questions/5018284/mysql-in-queries-terribly-slow-with-subquery-but-fast-with-explicit-values)
* [[慢查优化]慎用MySQL子查询，尤其是看到DEPENDENT SUBQUERY标记时 - 旁观者 - 博客园](http://www.cnblogs.com/zhengyun_ustc/p/slowquery3.html)
* [MySQL性能优化小记：MySQL子查询很慢的问题 - Bash @ Linux - ITeye博客](http://codingstandards.iteye.com/blog/1344833)
* [[慢查优化]慎用MySQL子查询，尤其是看到DEPENDENT SUBQUERY标记时 - 旁观者 - 博客园](http://www.cnblogs.com/zhengyun_ustc/p/slowquery3.html)
* 高性能mysql第三版 P.223