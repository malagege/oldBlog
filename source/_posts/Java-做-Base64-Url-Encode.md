---
title: Java 做 Base64 Url Encode
date: 2022-07-30 23:50:20
tags: [java,base64]
categories: Java
---

之前[有關 url 空白字符(%20和+) | 程式狂想筆記](https://malagege.github.io/blog/2020/03/18/%E6%9C%89%E9%97%9C-url-%E7%A9%BA%E7%99%BD%E5%AD%97%E7%AC%A6-20%E5%92%8C/)文章有特別紀錄 url 放入 + 會當作空白字符，所以才會知道 base64 encode 過資料放在url GET 參數會有問題。

<!--more-->

最近我使用base64 發現有這個問題，這邊網路查要怎麼使用，這邊小記一下。

https://httpbin.org/get?test=123+123

![](https://i.imgur.com/0hajVfj.png)



## Apache Commons

Base64.encodeBase64URLSafeString
程式可參考:[commons-codec/Base64.java at ebebbd427d80639692b2a06e51cc2885a1543b18 · apache/commons-codec · GitHub](https://github.com/apache/commons-codec/blob/ebebbd427d80639692b2a06e51cc2885a1543b18/src/main/java/org/apache/commons/codec/binary/Base64.java#L304)

很特別的是 Base64 URL encode 可以用 decodeBase64 去解析。