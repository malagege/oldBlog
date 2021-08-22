---
title: 我多年忽略了 Java RuntimeException 觀念
date: 2021-08-22 15:39:17
tags: [java,exception]
categories: Java
---

最近寫 Java 發現某個 MyBatis API 報錯，文件沒有寫會丟 `Exception`，但是 MyBatis DB 發生錯誤就會丟…，後來發現是我當出沒看好，所以趕快記錄一下。


<!--more-->

文章重點取自 [Java筆記 — Exception 與 Error. 這是個很老梗的問題了, 但每個階段回來看, 都會有不同的體會… | by Carl | Medium](https://medium.com/@clu1022/java%E7%AD%86%E8%A8%98-exception-%E8%88%87-error-dbdf9a9b0909)，建議看大神文章，可以呼略下面筆記。 備份圖

## Java 錯誤種類

>     Exception: 通常指程式運行時所出現的可預料之意外狀況, 基本上都要進行 catch 的動作, 然後進行相應處理, 如 IOException.
>    Error: 指在正常情況下, 不太可能出現的問題, 絕大部分的 Error 都會導致程式 (e.g. JVM 本身) 處於一種不正常且不可恢復的狀態. 所以對於這種情況, 你也不太需要去 catch 了, 因為也沒什麼意義. 常見的如 OutOfMemoryError / StackOverflowError 這些, 都是繼承自 Error.

## Java Exception(重點)

![](https://imgur.com/kZ0fgK7)

>    Checked Exception: 又稱受檢例外, 通常在原始碼中必須顯式地 catch 並且處理, 這部分算是 compile time 會檢查的部分.
>    Unchecked Exception: 又稱非受檢例外, 就是所謂的 RuntimeException, 常見的像是 NullPointerException, ArrayIndexOutOfBoundsException. 這種類型的例外通常是可以透過撰寫相應程式以避免的邏輯錯誤, 可以根據當下的情境來判斷是不是要 catch, 且在 compile time 並不會強制要求要 catch.

### 原因出在 RuntimeException

這邊`RuntimeException`程式執行時候才會丟 Exception ，這邊就要想想什麼時候要做 Exception ，說真的目前遇到公司都會丟 `throw Exception`(這邊是公司底層 Dao 都會丟 Exception)，編輯器都會跟你說要`add throw Exception 到 method`，因為丟會防止程式問題，要怎麼處理 RuntimeException 這是一個好問題，有時間再整理。


## 相關 RuntimeException 文章

- [菜鳥工程師 肉豬: Spring Web 處理存取資料庫例外錯誤 handle database access exception](https://matthung0807.blogspot.com/2020/06/spring-web-handle-database-access.html) [備份圖]

- [RuntimeException和Exception区别 - jtlgb - 博客园](https://www.cnblogs.com/jtlgb/p/5985120.html)

- [java - Why is catching a RuntimeException not considered a good programming practice? - Stack Overflow](https://stackoverflow.com/questions/24344511/why-is-catching-a-runtimeexception-not-considered-a-good-programming-practice)


## 日常接觸 Exception 處理

### Spring Boot Transactional

我常常在工作中會把 Spring Boot Transactional 交易改成`Exception`，公司底層也是這樣寫，是因為怕程式錯誤沒有 rollback 會造成這樣，但我想最正確寫法。

- [Hibernate 和 Spring Boot JPA 做 @Transactional 不一樣 session.close 時機 | 程式狂想筆記](https://malagege.github.io/blog/2020/09/06/Hibernate-%E5%92%8C-Spring-Boot-JPA-%E5%81%9A-Transactional-%E4%B8%8D%E4%B8%80%E6%A8%A3-session-close-%E6%99%82%E6%A9%9F/)
- [Spring 交易事務失效 | 程式狂想筆記](https://malagege.github.io/blog/2020/08/27/Hibernate-%E4%BA%A4%E6%98%93%E4%BA%8B%E5%8B%99%E5%A4%B1%E6%95%88/)
- [詳細瞭解 Spring Transaction 的 Propagation](https://www.tpisoftware.com/tpu/articleDetails/2092) 備份圖

## 感想

大概看了一些文章，處理 DB 應該不需要處理`RuntimeException`，也應該不需要 `Excepion`，也許你改成`Exception`時候代表程式沒有寫好(程式單一原則)，不該把`Runtime Exception`程式碼放在裡面。

我目前看到結果大概是這樣，有問題再補。


題外話，其實幾個月前我就有打算整理這篇，最近學 Lamdba 遇到 Exception 問題，所以想到這個還沒有整理，之後還要整理 Lamdba 怎麼處理 Exception 問題。