---
title: Mysql記錄時間的那些事情
date: 2019-01-10 22:04:08
tags: [mysql]
categories: MySQL
---

常時間我們都會記錄時間
很常大家都會使用`datetime`來記錄
有時候大家也很喜歡使用`unixtime`來做時間記錄
到底 DB 有沒有記錄什麼`時間`方法
這篇只是小記

<!--more-->

由於我現在公司使用 unixtime 型別存在資料庫是`int`
今天用 phpmyadmin 操作設定 ts 欄位(int10)做 default value 動做 `CURRENT_TIMESTAMP`
竟然不能 work!!!!
哪尼我要做 default 不能做
後來看到[MySQL Timestamp 型態 的 屬性(新增/修改 自動更新 Timestamp 型態 的 欄位) | Tsung's Blog](https://blog.longwin.com.tw/2007/10/mysql_timestamp_properties_2007/)
他是用 timestamp...，~~等等 timestamp 不是 int 嗎...~~
不過我看了一下 phpmyadmin 顯示是時間的日期
這邊我們先來看官方文件怎麼說?

## 官方文件

### String and Numeric Literals in Date and Time Context.

MySQL recognizes DATE values in these formats:

> - As a string in either 'YYYY-MM-DD' or 'YY-MM-DD' format. A “relaxed” syntax is permitted: Any punctuation character may be used as the delimiter between date parts. For example, '2012-12-31', '2012/12/31', '2012^12^31', and '2012@12@31' are equivalent.
> - As a string with no delimiters in either 'YYYYMMDD' or 'YYMMDD' format, provided that the string makes sense as a date. For example, '20070523' and '070523' are interpreted as '2007-05-23', but '071332' is illegal (it has nonsensical month and day parts) and becomes '0000-00-00'.
> - As a number in either YYYYMMDD or YYMMDD format, provided that the number makes sense as a date. For example, 19830905 and 830905 are interpreted as '1983-09-05'.

簡單來說 MySQL 來看時間'2018-12-12','20181212','2018@12@12' MySQL 都看得懂
'181212'他也會自動轉成'2018-12-12'
非字串數字也能轉成時間

好吧大多數來講這樣寫，可能會被公司長輩打頭(逃~~)

### MySQL recognizes DATETIME and TIMESTAMP values in these formats:

MySQL recognizes DATETIME and TIMESTAMP values in these formats:

> - As a string in either 'YYYY-MM-DD HH:MM:SS' or 'YY-MM-DD HH:MM:SS' format. A “relaxed” syntax is permitted here, too: Any punctuation character may be used as the delimiter between date parts or time parts. For example, '2012-12-31 11:30:45', '2012^12^31 11+30+45', '2012/12/31 11*30*45', and '2012@12@31 11^30^45' are equivalent.
> - The only delimiter recognized between a date and time part and a fractional seconds part is the decimal point.
> - The date and time parts can be separated by T rather than a space. For example, '2012-12-31 11:30:45' '2012-12-31T11:30:45' are equivalent.
> - As a string with no delimiters in either 'YYYYMMDDHHMMSS' or 'YYMMDDHHMMSS' format, provided that the string makes sense as a date. For example, '20070523091528' and '070523091528' are interpreted as '2007-05-23 09:15:28', but '071122129015' is illegal (it has a nonsensical minute part) and becomes '0000-00-00 00:00:00'.
> - As a number in either YYYYMMDDHHMMSS or YYMMDDHHMMSS format, provided that the number makes sense as a date. For example, 19830905132800 and 830905132800 are interpreted as '1983-09-05 13:28:00'.

簡單來說他跟上面點都差不多，第二點在說`date`跟`time`需要用一個符號區分開(矇逼，我沒看文件還真的不知道...)
不知道動手玩看看就知道

```sql
select unix_timestamp('2012@12@31');
select unix_timestamp('2012-12-31');
select unix_timestamp('20121231');
select unix_timestamp('20021231');
select unix_timestamp('021231');

select unix_timestamp('2012@12@31 010101');
select unix_timestamp('2012-12-31G010101');
```

PS: 兩位數轉換官方文件也有寫清楚

```
    Year values in the range 70-99 are converted to 1970-1999.

    Year values in the range 00-69 are converted to 2000-2069.
```

官方文件

- [MySQL :: MySQL 8.0 Reference Manual :: 11.3.1 The DATE, DATETIME, and TIMESTAMP Types](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)
- [MySQL :: MySQL 8.0 Reference Manual :: 9.1.3 Date and Time Literals](https://dev.mysql.com/doc/refman/8.0/en/date-and-time-literals.html)

更詳細的 datetime 跟 timestamp 差別請看[MySQL 案例之 Timestamp 和 Datetime - Rangle - 博客园](https://www.cnblogs.com/rangle/p/8275302.html) {% asset_link web1.png 備份圖 %}

我過去看過型別

- datetime
- timestamp
- int
- varchar

int 是現在公司用的
varchar 事前一間公司用的，聽說很舊系統都這樣用，聽說好移轉 XD

## 使用 unixtime 要注意的大小事 - 時區問題

如裡面有做備份說明[MySQL 案例之 Timestamp 和 Datetime - Rangle - 博客园](https://www.cnblogs.com/rangle/p/8275302.html)

不過我看一些紀錄都不建議用 timestamp 做紀錄
詳細如下

- [timezone - How do I get the current time zone of MySQL? - Stack Overflow](https://stackoverflow.com/questions/2934258/how-do-i-get-the-current-time-zone-of-mysql)
- [Mysql 的問題 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10186779)
- [MySQL 的时区是否应该设置为 UTC？ - 代码日志](https://codeday.me/bug/20170619/28746.html)
- [如何在 MySQL 中存储 datetime 与时区信息 - 代码日志](https://codeday.me/bug/20171201/103722.html)
- [sql-我应该在 MySQL 中使用 datetime 还是 timestamp 数据类型？——CSDN 问答频道](https://ask.csdn.net/questions/711972)

推估應該要以程式端為主控制(?!，目前自己猜測的...)

先記錄到這邊

題外話
想起之前想寫一篇 timezone format 筆記
但都沒有抽出時間寫`2014-12-01T12:00:00Z`
[[ Alex 宅幹嘛 ] 👨‍💻moment.js 日期時間管理資源庫入門導讀 - YouTube](https://www.youtube.com/watch?v=DDu78WvmpB0)
有時間來時做一個來玩玩看
另外瀏覽器的時間格式是`2118-01-01`
剛好發現...html 的 input type 屬性`datetime`屬性廢棄掉了
[<input type="datetime"> | MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/datetime)
[<input type="datetime-local"> | MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/datetime-local)
他的格式是`2018-06-12T19:30`
前端最近流行這個格式???
