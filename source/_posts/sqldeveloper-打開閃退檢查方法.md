---
title: sqldeveloper 打開閃退檢查方法
date: 2020-03-07 01:14:03
tags: [sqldeveloper, oracle]
categories: SQL
---

最近使用 sqldeveloper 開啟出現問題，瞬間打開會閃退
網路上查到一些原因
但都無法解決

<!--more-->

網路上有看到三種解決方法，這邊整理一下

## 抓舊版 jdk

這邊是網路大多數都是使用這個方法，但我無法解決。
我發現使用這個都是抓 sqldeveloper 不包含 jdk 版本
通常 jdk 版本不能太新(需要 1.6 版本)
還有需要設定 conf jdk 路徑
sqldeveloper.conf 設定 SetJavaHome
可參考[Oracle 11g自带的sqldeveloper.exe打不开问题_数据库_weixin_38569499的博客-CSDN博客](https://blog.csdn.net/weixin_38569499/article/details/80316277)

## 清除使用者暫存資料 


> In my case i had 2 folders under C:\Users\username\AppData\Roaming\
> 
>     sqldeveloper
>     SQL developer
> 
> after removing both of them and launching installation dir\sqldeveloper\sqldeveloper\bin\sqldeveloper64.exe it worked.


刪除 C:\Users\username\AppData\Roaming\ 裡面的 `sqldeveloper`,`SQL developer` 兩個資料夾
就可以正常運行

仔細想想一開始打開 sqldeveloper 我有移動過位置
不知道是不是這個關係，所以才會導致?

## 看到奇怪的解決方式

試過，我的不是這個問題

[SQL Developer 開啟失敗解決辦法 - IT閱讀](https://www.itread01.com/content/1544637086.html)

## Dbeaver 連 OJDBC 方法

Dbeaver 會導向官網叫你下載 OJDBC ，但這個有分版本
我因為懶惰使用 sqldeveloper 裡面 OJDBC (*.jar)
可以登入，但查詢 SQL 會有錯誤訊息

```
org.jkiss.dbeaver.model.sql.DBSQLException: SQL 错误: jdbc 驱动内部错误
	at org.jkiss.dbeaver.model.impl.jdbc.exec.JDBCStatementImpl.executeStatement(JDBCStatementImpl.java:134)
	at org.jkiss.dbeaver.ui.editors.sql.execute.SQLQueryJob.executeStatement(SQLQueryJob.java:486)
	at org.jkiss.dbeaver.ui.editors.sql.execute.SQLQueryJob.lambda$0(SQLQueryJob.java:424)
	at org.jkiss.dbeaver.model.exec.DBExecUtils.tryExecuteRecover(DBExecUtils.java:159)
	at org.jkiss.dbeaver.ui.editors.sql.execute.SQLQueryJob.executeSingleQuery(SQLQueryJob.java:416)
...
```

後來我發現，要去官網抓 ojdbc-full.tar.gz
匯出全部 jar ，去設定 Dbeaver 就能設定了

注意 OJDBC 也有分版本，要使用對應 Oracle 版本


## 想關連結

[Oracle 11g自带的sqldeveloper.exe打不开问题_数据库_weixin_38569499的博客-CSDN博客](https://blog.csdn.net/weixin_38569499/article/details/80316277)
[sql - SQLDeveloper not starting - Stack Overflow](https://stackoverflow.com/questions/22810318/sqldeveloper-not-starting)