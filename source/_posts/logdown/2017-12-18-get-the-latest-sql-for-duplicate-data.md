---
layout: post
title: '取得重覆資料最新SQL'
date: 2017-12-18 13:13
comments: true
categories: SQL
tags: ['sql']
---
在[Backend 台灣 (Backend Tw)](https://www.facebook.com/groups/616369245163622/permalink/1222070147926859/)看到
如下
```sql
SELECT m1.*
FROM messages m1 LEFT JOIN messages m2
 ON (m1.name = m2.name AND m1.id < m2.id)
WHERE m2.id IS NULL;
```
這寫法很有趣XD

> Triton Ho:
Group by是 O(n log n)
Left join 是O(n^2)

>George Li Chino Lin :
實際上我如果把資料降到10比左右, 這兩種query 的效能就沒有明顯差異～
http://sqlfiddle.com/#!9/296a6a/1
http://sqlfiddle.com/#!9/296a6a/2
但是筆數拉到300左右就有明顯差異了～
所以合理判斷資料量上升～ 效能差距會越明顯～
資料更大的話基本上沒辦法用這平台了ＸＤ

>Chino Lin:
我自己用一個表約100萬筆來測試
使用left join方式 跑到超過6分鐘沒等下去 ，
但是用子查詢groupby 大約1秒就跑完
後來改成4萬筆left join跑了2分15才跑完
子查詢+groupby也是大約1秒就跑完
我的是mysql5.6
那我看還是用子查詢+groupby就好.......
可能是5.0太舊不值得參考了

```sql
select *
from messages
where Id in (select max(id) from messages group by Name)
```

# 補充
可能是MySQL版本問題，正常應該要用Group by 效能會比較好，看到這種特別SQL滿好奇，但這不是最好的解法
純心賞看看就好，更多東西去[Backend 台灣 (Backend Tw)](https://www.facebook.com/groups/616369245163622/permalink/1222070147926859/)裡面看看
