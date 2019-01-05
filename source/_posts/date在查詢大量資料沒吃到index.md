---
title: date在查詢大量資料沒吃到index
date: 2018-12-24 20:58:44
tags: [sql, index]
categories: MySQL
---

最近做時間查詢一個月資料
但是盡然沒有吃到 index
我抓一天、兩天資料都有吃到
抓到超過 20 天竟然就吃不到
覺得這個問題好像滿嚴重

<!--more-->

## 大量資料吃 index 未必是壞事

記得之前去聽一個研討會
當欄位資料別少的時候(姓別：男、女)
但大量資料，資料分散造成讀取不是連續的
會造今資料不會很快
所以這時候 full scan 不是壞事

但今過爬文找到[Re: [SQL ] mysql date column index 問題 - 看板 Database - 批踢踢實業坊](https://www.ptt.cc/bbs/Database/M.1379428749.A.7E8.html)

```
首先你要知道 INDEX 是另外存的 也佔空間 也吃 FILE IO
也就是說 透過 INDEX 其實不一定比較快
例如你有一個 TABLE 總共佔了 10000 個 block
然後建了一個 index , 這個 index 本身佔了 2000 個 block
假設你今天要找的資料 在整個 table 的 10000 個 blocks 裡，
散布在其中的 9000 個 blocks 裡面。

這時候  你透過 index 找資料的 IO 成本就會是 2000 + 9000 = 11000
而 Full Table Scan 的 IO 成本只有 10000
像這樣的時候， DBMS 就會選擇直接做 Table Scan 因為成本比較低。
那 DBMS 怎麼知道哪條路成本比較低呢
就來自它本身對這些 表格/索引 的統計值

以 Oracle 舉例  跟這樣行為有關的參數就包括有
1. 最佳化器嘗試組出最佳路徑的嘗試次數上限
2. 最佳化器判斷 走 index 的成本 大於 Full Table Scan 成本的多少比例以上
   就走 Full Scan
3. 最佳化器優化的方向(最小IO , 最快回應等
回到你提的問題
從你下的參數中我們可以注意到幾件事


1. 在你指定的範圍內資料量較小的時候 DBMS 會覺得該走 index

2. 在你指定的範圍內資料量較大的時候 DBMS 選擇走 FULL SCAN

3. 你下了 select * 這代表所有不在INDEX上的欄位，DBMS都必須要翻
   DATA BLOCKS出來才能拿到資料

但是在你的資料中  0000-00-00  的資料量到底有多少？

如果只有很小一點點 (或是 DBMS 覺得它只佔一點點

那麼使用 FULL SCAN 是很正常的。

反正你都得翻這些資料出來，何必脫褲子放屁去 index blocks 再繞一圈呢？


但是如果 這樣的資料是佔多數的

那或許是你的表格/索引的統計值出問題了。導致 DBMS 覺得這樣比較快


* 有另一個狀況 就是  DBMS 在處理 function(col) 這些操作的時候
  因為存在 index 裡的資料是 col 而非 function(col)
  所以 DBMS 會覺得這個 index 是與需求不符的 會放棄

** 然後 資料量大的話 會慢是正常的啊 有吃 IO 吃CPU 就有開銷就是要花工夫啊
   你只能避免它繞冤枉路 該走的路還是免不了的

```

其實 MySQL 被評效能不好，不過看樣子是 DB 選擇最佳方案
看到這個就讓他放手去做 full scan
不然就做大數據 XD(其實我不會)

{% asset_img 1.jpg  %}
{% asset_img 2.jpg  %}

## 強制做 index 查詢

```
select count(*) from http_log_3 force index(time) where time >= 000000
```

**注意** 這邊的 index(`index名字`)
如我上面圖片要寫成 : force index(`idx_date`)

這邊有範例[通過 force index 了解的 MySQL 查詢的性能優化 | 不分享空間](https://et3w503.wordpress.com/2008/12/02/%E9%80%9A%E9%81%8Eforce-index%E4%BA%86%E8%A7%A3%E7%9A%84mysql%E6%9F%A5%E8%A9%A2%E7%9A%84%E6%80%A7%E8%83%BD%E5%84%AA%E5%8C%96/)
可以看到跑出來結果差很多
所以我們還是要相信 MySQL XDD

[Mysql 的强制索引（Force Index）都为我们做了哪些优化？ - 愿今日的一切困难在未来都不值一提 - ITeye 博客](https://jef.iteye.com/blog/386907)

其他問題小記
[老天尊的死期: MySQL 常見問題](http://carlislebear.blogspot.com/2013/06/mysql.html)
[mysql 的 process 堵塞处理 | Sayue's Blog](https://sayue.me/2017/06/11/mysql%E7%9A%84process%E5%A0%B5%E5%A1%9E%E5%A4%84%E7%90%86/)
