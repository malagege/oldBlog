---
title: 使用 OWASP Enterprise Security API (ESAPI) 解決 SQL Injection 問題
date: 2022-07-31 02:31:08
tags: [owasp,esapi,injection]
categories: 源碼掃描
---


參考：
- [GitHub - ESAPI/esapi-java](https://github.com/ESAPI/esapi-java)
- [JAVA 安全编程——ESAPI与OWASP Top 10 - 简书](https://www.jianshu.com/p/cab2ba5e5ca5)
- [SQL Injection Prevention - OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)

<!--more-->

## 四種解決方法

裡面有提到，正常有用安全性處理參數方法就能解決這個問題。沒有的話使用[Defense Option 4: Escaping All User-Supplied Input](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html#defense-option-4-escaping-all-user-supplied-input)解決問題，程式邏輯上通常不會大改。

## Jar 位置

1. [Maven Repository: org.owasp.esapi » esapi » 2.2.3.1](https://mvnrepository.com/artifact/org.owasp.esapi/esapi/2.2.3.1)

2. 放置 ESAPI.properties

## 需要放置 ESAPI.properties

![](https://i.imgur.com/xUvtMdb.png)


> Yeah, you can get rid of those messages by running it as:
> java -Dorg.owasp.esapi.logSpecial.discard=true ...
> 
> So, this is not a bug, but a feature. See https://javadoc.io/doc/org.owasp.esapi/esapi/latest/org/owasp/esapi/reference/DefaultSecurityConfiguration.html for details of how ESAPI searches for the ESAPI.properties file.

沒有預設設定...

https://github.com/ESAPI/esapi-java-legacy/issues/653#issuecomment-1016995464


當然原官方上面也有放，但預設抓不到這個地方。
https://github.com/ESAPI/esapi-java-legacy/blob/develop/configuration/esapi/ESAPI.properties


可放置專案的`src\esapi`當然這邊看專案設定路徑
或者可放入參數，詳細可看官網或log設定
`src\esapi\ESAPI.properties`、`src\esapi\validation.properties`


## 小記

最後做源碼掃描就沒有被掃描出來，可喜可賀。