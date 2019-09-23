---
title: MySQL QUOTE() 跳脫字元小記
date: 2019-09-10 20:23:17
tags: [mysql]
categories: MySQL
---

最近找 MySQL 有什麼跳脫字元函式
有爬到 `QUOTE()` 可以用
但比較特別

<!--more-->

```
mysql> SELECT QUOTE('w3re''source');
+-----------------------+
| QUOTE('w3re''source') |
+-----------------------+
| 'w3re\'source'        | 
+-----------------------+
1 row in set (0.03 sec)
```

[MySQL QUOTE() function - w3resource](https://www.w3resource.com/mysql/string-functions/mysql-quote-function.php)

## 注意事項

- `NULL` 會回傳 NULL 字串!!! 這一點還滿神奇的
- 字串會被`'`包住

透過這種特性，我可以用 SQL 組我要的字串

### 組 sql 範例

```sql
select concat("UPDATE oo SET a1 = ",quote(a1), ", a2 = ",quote(a2), " WHERE id =", bbb.id,";")
FROM xxxx
inner join bbb on ........
```

組完可以匯出 csv
可以用我之前寫的[malagege/sqlMakecsv: for a sqltocsv package easy sample](https://github.com/malagege/sqlMakecsv)
這時候會發現 csv 內容每一行前後都會有 `"`
一行式 SQL 可以用正規化解決

但是多行的呢?
我想到解決方法

1. 用csv2xlsx 轉 xlsx [csv2xlsx CSV 轉 Excel 方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/08/26/csv2xlsx-CSV-%E8%BD%89-Excel-%E6%96%B9%E6%B3%95/)

xlsx 複製裡面內容出來  發現還是有 `"`

2. 開啟 Word 把所有內容`選擇性天上→超連結貼上`上面，即可解決!!!

`QUOTE`跳脫字元函式可以解決`"`,`'`問題