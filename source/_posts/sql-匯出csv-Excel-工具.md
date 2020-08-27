---
title: 'sql 匯出csv ,Excel 工具'
date: 2020-08-24 22:32:48
tags: [sql, csv, excel]
categories: 實用工具
---

~~之前有寫過~~[malagege/sqlMakecsv: for a sqltocsv package easy sample](https://github.com/malagege/sqlMakecsv)
嚴格說是我改別人 code 改寫
簡單陽春寫出 sql 連 MySQL(或 Golang 有支援的DB Driver) 轉 csv, Excel
注意: 撈出幾百萬資料可能會造成記憶體吃光光
可能需要手動分別撈出來

<!--more-->

## 自己寫的工具

1. [malagege/sqlMakecsv: for a sqltocsv package easy sample](https://github.com/malagege/sqlMakecsv)


最近公司環境 DB 是用 Oracle
我當初寫沒有塞 Oracle Driver
最後有查到兩個方式

[gdbc/wrapper at master · identitii/gdbc](https://github.com/identitii/gdbc/tree/master/wrapper)
1. OJDBC (GDBC) 但 Windows WSL 編譯失敗
中途發現它編譯還需要改 pom.xml 的 http → https
裡面有 native-image 指令
後來發現需要裝[GraalVM 介紹 + 安裝教學](https://kucw.github.io/blog/2019/10/java-graalvm/)
好險 sdkman 可以搞定這一切
不過後來排除各種問題
還是失敗

~~沒有在 Linux 環境安裝~~
~~所以有空再試試~~
編譯出來還是失敗
```
Error: Classes that should be initialized at run time got initialized during image building:
 java.sql.DriverManager was unintentionally initialized at build time. To see why java.sql.DriverManager got initialized use -H:+TraceClassInitialization

com.oracle.svm.core.util.UserError$UserException: Classes that should be initialized at run time got initialized during image building:
 java.sql.DriverManager was unintentionally initialized at build time. To see why java.sql.DriverManager got initialized use -H:+TraceClassInitialization

	at com.oracle.svm.core.util.UserError.abort(UserError.java:68)
	at com.oracle.svm.hosted.classinitialization.ConfigurableClassInitialization.checkDelayedInitialization(ConfigurableClassInitialization.java:526)
	at com.oracle.svm.hosted.classinitialization.ClassInitializationFeature.duringAnalysis(ClassInitializationFeature.java:227)
	at com.oracle.svm.hosted.NativeImageGenerator.lambda$runPointsToAnalysis$8(NativeImageGenerator.java:732)
	at com.oracle.svm.hosted.FeatureHandler.forEachFeature(FeatureHandler.java:70)

```
這邊有 OJDBC 8 解決方法，但我看 GDBC 好像是用 6
不過我不是很熟，所以就沒繼續研究
https://github.com/quarkusio/quarkus/issues/1658#issuecomment-523618130



2. 原生 Oracle 連線
oci8
[Oracle Instant Client Downloads | Oracle 台灣](https://www.oracle.com/tw/database/technologies/instant-client/downloads.html)
不過不想再 window 上面安裝東西
在 WSL 他又認得是 Window
所以放棄這個方案


Oracle 目前還是不能匯出
有空再做測試處理

最後還是放棄測試，因為可能就算可以執行
程式搬到別台但腦能不能跑也是一個問題


## export2excel

[tinywall/export2excel: Export Mysql/Oracle Data to Excel CSV](https://github.com/tinywall/export2excel)

在 Github 看到有人寫好的好用工具
這個就能使用 OJDBC 成功連到 Oracle
可以解決我現階段撈出報表問題

這邊要設定 Run.bat
set path="C:\Program Files\Java\jdk1.7.0_25\bin"

如果是在 Linux 記得要調整

```bash
# window
#javac -d classes -classpath lib\jdom-1.0.jar;lib\poi-3.0-rc4-20070503.jar;lib\ojdbc14.jar;lib\mysql-connector-java-5.1.6.jar; src/*.java

# Linux
# classpath 要改成 / 
# -jar 分號記得要改成冒號 :
javac -d classes -classpath lib/jdom-1.0.jar:lib/poi-3.0-rc4-20070503.jar:lib/ojdbc14.jar:lib/mysql-connector-java-5.1.6.jar src/*.java
```


CLOB 記得要轉乘 TO_CHAR
裡面程式沒有做 CLOB 處理