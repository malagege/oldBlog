---
title: MySQL 我對 timestamp 多年的誤解
date: 2019-09-23 20:11:43
tags: [mysql]
categories: MySQL
---

以前實作上我都使用 datetime 
沒有選過用 timestamp
今天我在找程式問題
後來發現我多年對 timestamp 多年誤解

<!--more-->

公司使用 int(10) 當 timestamp
所以我常常在 phpmyadmin 看到數值的東西

最近，剛好跟別的公司合作
剛好有一個資料表設計是 timestamp
由於我在 phpmyadmin 看到 時間格式資料，我以為是 datetime 格式
但我跟另外一個資料表裡面用 `datetime`
想說時間怎麼對不起來...

後來發現是 timestamp 是使用 unixtime 時間...(這個我知道)
我以為 timestamp 是用數字
**現在我知道他也是放`時間格式`**
**現在我知道他也是放`時間格式`**
**現在我知道他也是放`時間格式`**



另外我實驗這種也能順利存到 DB 去
```
INSERT INTO test(ts) values(19900101000000);
```

當然這個時間格式會隨著`@@session.time_zone` 做改變

```
set @@session.time_zone = '+08:00';
select * from test;
```

難怪，之前合作公司說 DB 記得要用 UTC 時間
因為這個主機上面有很多人在用，為了方便時間亂掉
不過使用 timestamp 方式相信做多國跨域網站真的會比較方便!!