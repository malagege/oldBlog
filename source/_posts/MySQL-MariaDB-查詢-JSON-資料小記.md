---
title: MySQL / MariaDB 查詢 JSON 資料小記
date: 2019-09-03 20:47:35
tags: [mysql,mariadb,json]
categories: MySQL
---

最近剛好要撈出某一台 DB 特別的值
但那個欄位都是 JSON 格式
剛好那台 DB 是 MySQL 5.7.x
可以用 JSON，就趁個這個機會做筆記


<!--more-->


```SQL
-- example
SELECT log->'$.uid' AS uid FROM xxx where log->'$.uid' IN (
    xxxx
)and time BETWEEN '2019-07-09 00:00:00' and '2019-07-09 23:59:59'

-- 暫時撈資料跑報表
SELECT type,pl_id,count(1)
FROM (
SELECT log->'$.uid' as uid,'2' as type,log->'$.playtrackId' AS pl_id, time  FROM log_table where log->'$.uid' = 'xxxxxx' and time between '2019-07-09 00:00:00' and '2019-07-09 23:59:59'
UNION ALL
SELECT log->'$.uid' AS uid,'2' as type,log->'$.playtrackId' AS pl_id, time  FROM log_table where log->'$.uid' = 'oooooo' and time between '2019-07-09 00:00:00' and '2019-07-09 23:59:59'
UNION ALL
SELECT log->'$.uid' AS uid,'2' as type,log->'$.playtrackId' AS pl_id, time  FROM log_table where log->'$.uid' = 'aaaaaa' and time between '2019-07-09 00:00:00' and '2019-07-09 23:59:59'
UNION ALL
SELECT log->'$.uid' AS uid,'2' as type,log->'$.playtrackId' AS pl_id, time  FROM log_table where log->'$.uid' = 'zzzzzz' and time between '2019-07-09 00:00:00' and '2019-07-09 23:59:59'
)  aa
group by type,pl_id
```

最後發現 JSON 字串前後會帶`"`
可以用`JSON_UNQUOTE`

MariaDB 也可以使用，不過我目前沒有用到
下方聯結有更詳細介紹
[MySQL 5.7 新特性 JSON 的创建，插入，查询，更新](http://www.lnmp.cn/mysql-57-new-features-json.html) {% asset_link web1.png 備份圖 %}
[MariaDB 10.0.X中，动态列支持 JSON 格式来获取数据。 -贺春旸的技术博客-51CTO博客](https://blog.51cto.com/hcymysql/1694181) {% asset_link web2.png 備份圖 %}

之後還有用到實用的方法再補充

