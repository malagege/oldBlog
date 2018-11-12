---
title: "遇到Data truncated for column 'd' at row 和改sql_mode那些事"
date: 2018-09-01 11:25:46
tags: [sql,mysql]
categories: MySQL
---

最近公司報表出問題了
繼上次DB還原[MySQL在Linux下預設有分大小寫和Incorrect integer value: '' for column 'num' 問題 | 程式狂想筆記](https://malagege.github.io/blog/2018/08/28/MySQL%E5%9C%A8Linux%E4%B8%8B%E9%A0%90%E8%A8%AD%E6%9C%89%E5%88%86%E5%A4%A7%E5%B0%8F%E5%AF%AB%E5%92%8CIncorrect-integer-value-for-column-num-%E5%95%8F%E9%A1%8C/)，有些設定跑掉
這次出現這個`Data truncated for column 'd' at row`
因為重新設定還原sql_mode是嚴格模式...

<!--more-->

為什麼這次會出現`Incorrect integer value: '' for column 'num'`
不知道這次也是Excel匯入也出現`Data truncated for column 'd' at row `
為什麼有不一樣的結果呢??
發現帶入空字串輸入到double會發這種事情...
導致可能前人寫的Excel匯入會有狀況

有簡單用docker hub pwd(play with docker)測試
{% asset_img error.jpg title %}

簡單記錄測試紀錄
再開嚴格模式
`'1'`都可以正常存入，但前後多不明空白會不能寫入
但沒開嚴格模式時候
`' 1'`不能正常寫入
`'1 '`可以正常寫入
當然他也沒有很聰明`''`跟`'NULL'`會變NULL

可能要改sql_mode改成沒嚴格模式
不過嚴格模式，主管擔心要重啟MySQL
所以也不能隨意重啟orz

最後網路上有爬到[XYZ的筆記本: MySQL Server 的 SQL Modes](https://xyz.cinc.biz/2013/08/mysql-server-sql-modes.html)
>SQL Modes 預設值：
在 5.6.5 和之前的版本，預設值是空的，沒有設置。
在 5.6.6 和之前的版本，預設值是 NO_ENGINE_SUBSTITUTION。
查詢目前 SQL Modes 目前的設定：
SELECT @@GLOBAL.sql_mode;
SELECT @@SESSION.sql_mode;
GLOBAL.sql_mode 是目前伺服器的設定值，SESSION.sql_mode 是目前連線的設定值
設定 SQL Modes 方法有以下三種：
1.修改設定檔(my.cnf或my.ini)中的 sql-mode="設定值"
2.MySQL啟動時，加參數 --sql-mode="設定值"
3.連線登入後修改
SET GLOBAL sql_mode='設定值';
SET SESSION sql_mode='設定值';
GLOBAL 是修改目前伺服器的設定值，SESSION 是修改目前連線的設定值

看到下面的`3.連線登入修改`，就謝天謝地
當然也要注意重新啟動，記得要改my.cnf

```
# /etc/mysql/my.cnf

[mysqld] 
sql_mode = NO_ENGINE_SUBSTITUTION
```

不用這個方法改code不知改道何時才會改完...


最後新專案開發用到MySQL還是啟用
比較不會出問題
```
[mysqld]
sql_mode='ONLY_FULL_GROUP_BY,NO_AUTO_VALUE_ON_ZERO,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,
ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION,PIPES_AS_CONCAT,ANSI_QUOTES'
```
參照來源:[mysql的sql_mode合理设置](http://xstarcd.github.io/wiki/MySQL/MySQL-sql-mode.html)

感覺比較不會出甚麼嚴重問題
