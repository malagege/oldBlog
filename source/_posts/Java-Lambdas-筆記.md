---
title: Java Lambdas 筆記
date: 2022-07-31 02:27:16
tags: [java,lambdas]
categories: Java
---

寫到一半沒寫完。

<!--more-->

一直不太了解 Java 使用 Lambdas，但每次學久久沒碰後都忘記要怎麼用，Java Lambdas 不像 JavaScript 的 Lambdas 難使用，Java 那邊還要了解 Predirect 類似這些東西

## 一直不太能接受 Lamdbas 為什麼能同等 Predicate

1. 了解為什麼 Java 的 Interface 可以用 Lamdbas 使用?

因為 `@FunctionalInterface`關係，可以看篇部分[关于Java Lambda表达式看这一篇就够了](https://objcoding.com/2019/03/04/lambda/#%E8%87%AA%E5%AE%9A%E4%B9%89%E5%87%BD%E6%95%B0%E6%8E%A5%E5%8F%A3)，裡面有簡單範例。

那些 Interface 可以用 Lamdbas ，看以看官方文件[FunctionalInterface (Java Platform SE 8 )](https://docs.oracle.com/javase/8/docs/api/index.html?java/lang/FunctionalInterface.html)。還滿多可以用的。

2. 為什麼 Lamdbas 知道你要用的裡面哪個Method?

一般我們建立 Tread 需要實立 Runabble 介面，我們都知道會跑 Runable的 run ；一般 Predicate 介面，知道是跑 test。奇怪一個 interface 怎麼知道我要跑哪個 method呢?

其實我有一個很簡單的想法，我們會發現 Runnable 和 Predicate 透過實例 Method 去呼叫，如: ArrayList.removeIf( __Predicate__) 有實立方法，裡面 `removeIf`只會呼叫`test`，就這麼簡單。 但是不會有呼叫多個 Methods嗎?應該是不會的，我目前看這些 Interface 實立 Method 時候都是只會呼叫一個，也許這也是程式優良的地方，單一原則。


知道以上幾點，就不會懷疑自己lamdbas 會跑哪一個 method 就不會想太多。

[Lambda Expressions (The Java™ Tutorials > Learning the Java Language > Classes and Objects)](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)

[六角鼠年鐵人賽 Week 16 - Spring Boot - 番外篇 Java 8 Lambda Tutorial - HackMD](https://hackmd.io/@KaiChen/SJ0wl8Nc8)


## Exception 處理




[java 8 lambda表达式中的异常处理 - flydean - 博客园](https://www.cnblogs.com/flydean/p/java-8-lambda-exception.html) [備份圖](https://i.imgur.com/woVUmtG.png)

[learn-java-streams/lambda-exception at master · ddean2009/learn-java-streams · GitHub](https://github.com/ddean2009/learn-java-streams/tree/master/lambda-exception)