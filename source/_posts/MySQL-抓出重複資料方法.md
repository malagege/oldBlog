---
title: MySQL 抓出重複資料方法
date: 2019-08-21 19:49:37
tags: [mysql]
categories: MySQL
---

最近收到要抓出資料重複(資料量很大)
這看起來很簡單，但沒想到讓我想了很久要怎麼做
最後實作出來筆記一下

<!--more-->

## EXCEL 過濾法

可以透過 Excel 抓重複資料
教學: [Excel 找出重複內容，標示或刪除相同資料教學 - G. T. Wang](https://blog.gtwang.org/windows/excel-find-highlight-delete-duplicate-row/)
之後可以透過 Excel 篩選過濾抓出來(或刪掉)

~~但我發現我要查詢重複條件有2個以上，所以這招無效~~
我剛剛想到用兩個欄位尋找重複資料，再篩選多個資料
{% asset_img 1.png Excel抓取重複資料 %}

{% asset_img 2.png 篩選多個資料 %}

~~其實能不用寫程式方法最好~~

## SQL 簡單反查法

不推薦使用，但適合少資料使用
我們知道 MySQL 做重複資料可以用 `group by` 和 `having count(1) > 1`
但是資料表唯一值`id`就會被過濾掉
這不是我們要的

可以透過重複欄位做 or 反查
但是我資料表資料太多
這招不行啊!!!

## JOIN 方法

這一招可以簡單抓到我要的資料
但是代價就是需要時間跑

```sql
select a.id,
a.xxxxxx
(select xxxxxx from oooo r where id = a.vvvv) as v, 
a.xxx,
a.xxxx
(Select count(1) from s where s.vvv = a.vvvvv) count,
(Select SUBSTRING(replace(min(i),'-',''),1,3) from s2  where s2.id = a.id) as s2_t
from a1  a  inner join a1 b on a.status="active" and  a.name = b.name and a.note = b.note and a.id != b.id 
group by a.id
```


## 最正規的作法

有查到可以用大數據的 MapReduce 
感覺這種東西應該應用在大數據
只是我沒研究，就先放著

[Mongo入门：数据去重之MapReduce，Aggregation的简单使用（另附相关网络资源） - V_code的博客 - CSDN博客](https://blog.csdn.net/V_code/article/details/54093671)



## Excel 備註小記

上述因為要抓多個資料，可以先跑一次
匯入到自己 localhost DB ，多個條件可以做index，這樣搜尋不用花太多時間

### csv檔案編碼

DB 是 UTF8 ， CSV 需要用 notepad++ 和 visual studio code 轉 Big5
否則 Excel 開會亂碼

### 多國語系貼到 Excel 要注意的事項

可以用 Excel 會入 csv 載入方式 (但需要注意有沒有資料有換行符號，可以先人工把放行用掉，正常應該不會遇到，但我資料有這樣orz)
匯入建議把所有資料變成`文字`，不然會發生資料跑錯欄位情形



其他相關連結:

[[MYSQL]利用 SQL 找出欄位值重覆的記錄 @ 紀錄些小事情 :: 痞客邦 ::](https://killworm737.pixnet.net/blog/post/21934570-%5Bmysql%5D%E5%88%A9%E7%94%A8-sql-%E6%89%BE%E5%87%BA%E6%AC%84%E4%BD%8D%E5%80%BC%E9%87%8D%E8%A6%86%E7%9A%84%E8%A8%98%E9%8C%84)
[從MYSQL資料庫中找出重複的資料並刪除 @ 緒含的部落格 :: 痞客邦 ::](https://senkao.pixnet.net/blog/post/28556740-%E5%BE%9Emysql%E8%B3%87%E6%96%99%E5%BA%AB%E4%B8%AD%E6%89%BE%E5%87%BA%E9%87%8D%E8%A4%87%E7%9A%84%E8%B3%87%E6%96%99%E4%B8%A6%E5%88%AA%E9%99%A4)