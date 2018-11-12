---
title: 用MySQL抓多列取起始時間與結束時間用法
date: 2018-05-25 23:27:00
tags: [sql,MySQL]
categories: MySQL
---

最近有專案要做，舊有的table記錄會員展期時間都在不同列
但是同時抓不同列的頭尾，要怎麼抓聽
前一間公司有做過切帳，但是...那時候切帳有頭尾分開
這次是連在一起
同尾跟上一個不連續代表有展期是分開的
就想想這張table有什麼方法可以做到
<!--more-->

想了一連串方法
用union all把starttime和endtime欄位串起來
做`count()`做數量加總
having過濾需要長度1的
代表
* 基數行是展期開頭
* 偶數行是展期結束

網路爬了一下MySQL要怎麼做Rank
[[数据库与SQL] - No.5 MYSQL实现 RANK函数排序功能 - CSDN博客](https://blog.csdn.net/tjuyanming/article/details/77825875)
[MySQL 求名次的問題 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10069933)

接下來序列都做好了，
原本想用1,0,1,0來做續接的方法
但是都失敗了

不過最後想到一個方式串接
最近看「SQL語法範例辭典」P.516 Between ,一個資料表當做不同資料表
實例都還滿有趣的
這個跟上一間切帳有種同工之妙

最後可以在用user跟這張表用between串起來...
不過用這個東西SQL可能特別長....

`where aa.rank+1 = bb.Rank AND aa.Rank%2 = 1;`
aa.rank+1 = bb.rank
rank對2對3，我一直記錯
(2) + 1 = 3 

把偶數行用消失，就rank % 2 = 1就好了

最後怕效能，所以在MySQL索引加入uid為index
但是竟然還是full scan
~~這又是另外一個故事了XD~~
最後用join方式解決出問題(解法跟上次MySQL IN解決方法差不多)
有空再開筆記






```sql
create table test_tb
(
	id int auto_increment
		primary key,
	uid varchar(10) null,
	starttime int null,
	endtime int null
)
comment 'test'
;

create index test_tb_uid_index
	on test_tb (uid)
;

-- TRUNCATE  test_tb;

INSERT INTO  test_tb(uid,starttime,endtime)VALUES ('a',unix_timestamp('2013-01-01'),unix_timestamp('2013-02-02'));
INSERT INTO  test_tb(uid,starttime,endtime)VALUES ('a',unix_timestamp('2013-02-02'),unix_timestamp('2013-03-01'));
INSERT INTO  test_tb(uid,starttime,endtime)VALUES ('a',unix_timestamp('2013-04-01'),unix_timestamp('2013-07-01'));
INSERT INTO  test_tb(uid,starttime,endtime)VALUES ('a',unix_timestamp('2013-07-01'),unix_timestamp('2013-09-01'));
INSERT INTO  test_tb(uid,starttime,endtime)VALUES ('b',unix_timestamp('2013-01-01'),unix_timestamp('2013-02-01'));
INSERT INTO  test_tb(uid,starttime,endtime)VALUES ('b',unix_timestamp('2013-03-01'),unix_timestamp('2013-04-01'));
INSERT INTO  test_tb(uid,starttime,endtime)VALUES ('b',unix_timestamp('2013-04-01'),unix_timestamp('2013-05-01'));
```

```sql
-- query
select *
from test_tb;;

SELECT *
FROM
(
SELECT @rownum := @rownum+1 AS 'Rank', t
 FROM
   (SELECT from_unixtime(time) t
    FROM
      (
        select starttime as 'time'
        from test_tb
        WHERE uid = 'a'
        union ALL
        SELECT endtime as 'time'
        FROM test_tb
        where uid = 'a'
      )  a
    group by time
    HAVING  count(1) = 1
   ) a, (SELECT @rownum := 0) r
) aa,(
       SELECT @rownum1 := @rownum1+1 AS 'Rank', t
       FROM
         (SELECT from_unixtime(time) t
          FROM
            (
              select starttime as 'time'
              from test_tb
              WHERE uid = 'a'
              union ALL
              SELECT endtime as 'time'
              FROM test_tb
              where uid = 'a'
            )  a
          group by time
          HAVING  count(1) = 1
         ) a, (SELECT @rownum1 := 0) r
     ) bb

 where aa.rank+1 = bb.Rank AND aa.Rank%2 = 1;

EXPLAIN  SELECT *
FROM
(
SELECT @rownum := @rownum+1 AS 'Rank', t
 FROM
   (SELECT from_unixtime(time) t
    FROM
      (
        select i.starttime as 'time'
        from test_tb i inner join test_tb ii
        WHERE ii.uid = 'a'
        union ALL
        SELECT i.endtime as 'time'
        FROM test_tb i inner join test_tb ii
        where ii.uid = 'a'
      )  a
    group by time
    HAVING  count(1) = 1
   ) a, (SELECT @rownum := 0) r
) aa,(
       SELECT @rownum1 := @rownum1+1 AS 'Rank', t
       FROM
         (SELECT from_unixtime(time) t
          FROM
            (
              select i.starttime as 'time'
              from test_tb i inner join test_tb ii
              WHERE ii.uid = 'a'
              union ALL
              SELECT i.endtime as 'time'
              FROM test_tb i inner join test_tb ii
              where ii.uid = 'a'
            )  a
          group by time
          HAVING  count(1) = 1
         ) a, (SELECT @rownum1 := 0) r
     ) bb

 where aa.rank+1 = bb.Rank AND aa.Rank%2 = 1;
```
[SQL Fiddle](http://sqlfiddle.com/#!9/eb812b/3)
