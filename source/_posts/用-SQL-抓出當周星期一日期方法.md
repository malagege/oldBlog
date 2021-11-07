---
title: 用 SQL 抓出當周星期一日期方法
date: 2021-11-07 22:11:22
tags: [sql]
categories: SQL
---

最近維運常常每周都用SQL抓前一個禮拜內容，日期都是手動調整，長久下來都會怕忘記調整，最近再找有什麼方法可以抓到當周的禮拜一，爬文還真的有找到，趕快紀錄下來。


<!--more-->


## MySQL

```sql=
SELECT DATE_ADD(CURDATE(), INTERVAL - WEEKDAY(CURDATE()) DAY)
```

```sql=
select date(curdate() - interval weekday(curdate()) day)
```



參考:[MySQL: select date of current week's monday | Newbedev](https://newbedev.com/mysql-select-date-of-current-week-s-monday)

### 前一個禮拜一

```sql=
select date(curdate() - interval weekday(curdate()) day - interval 1 week)
```

### 番外

If you count Monday as the first day of the week:

```sql=
    SELECT STR_TO_DATE(CONCAT(YEARWEEK(NOW(), 1),'Monday'), '%x%v %W');

```

If you count Sunday as the first day of the week:

```sql=
    SELECT STR_TO_DATE(CONCAT(YEARWEEK(NOW()),'Monday'), '%X%V %W');

```


## Oracle

```sql=
select trunc(sysdate,'IW') from dual;
```


參考:[First Date of Week(Monday Date) - Ask TOM](https://asktom.oracle.com/pls/apex/asktom.search?tag=first-date-of-weekmonday-date)

比較有趣這邊是用 ISO 日期，ISO 日期第一天是 Monday ，


## Excel


```

The following will display the dates for Monday, Wednesday and Friday of the current week:

=NOW() - WEEKDAY(NOW(),3)
=NOW() - WEEKDAY(NOW(),3)+2
=NOW() - WEEKDAY(NOW(),3)+4
```

```
Sunday of this current week:

=1-WEEKDAY(TODAY())+TODAY() 
Monday of this current week:

=2-WEEKDAY(TODAY())+TODAY() 
Tuesday of this current week:

=3-WEEKDAY(TODAY())+TODAY() 
Wednesday of this current week:

=4-WEEKDAY(TODAY())+TODAY() 
Thursday of this current week:

=5-WEEKDAY(TODAY())+TODAY() 
Friday of this current week:

=6-WEEKDAY(TODAY())+TODAY() 
Saturday of this current week:

=7-WEEKDAY(TODAY())+TODAY() 
SUNDAY of following week, when Monday & not Sunday is used as day #1 of week:

=8-WEEKDAY(TODAY())+TODAY()
Also, if you change the format of the cell to a custom format and choose DDD it will display Fri or Mon and if you use DDDD it will display Friday or Monday, etc.
```

這邊看到覺得滿有趣，先記錄[How to get the dates of the current monday, wednesday and friday of the current week in excel? - Super User](https://superuser.com/questions/376698/how-to-get-the-dates-of-the-current-monday-wednesday-and-friday-of-the-current)

## PostgreSQL


```sql=
SELECT current_date - ((6 + cast(extract(dow FROM current_date) AS int)) % 7)
```


[postgresql - Get this week's monday's date in Postgres? - Stack Overflow](https://stackoverflow.com/questions/27989762/get-this-weeks-mondays-date-in-postgres/27990193)