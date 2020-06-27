---
title: 有關 url 空白字符(%20和+)
date: 2020-03-18 22:17:56
tags: [html]
categories: HTML
---

最近做 js 測試時後發現
我寫這麼久才注意到這個東西
這邊小記一下

簡單來說`%20`和(`+`)加號都是變成`空白鍵`
相反的`%2B`會變成`+`

http://httpbin.org/get?test=123%20123

動手測試比較快

<!--more-->

> 當HTML表單中的數據被提交時，表單的域名與值被編碼並通過HTTP的GET或者POST方法甚至更古遠的email[2]把請求發送給伺服器。這裡的編碼方法採用了一個非常早期的通用的URI百分號編碼方法，並且有很多小的修改如新行規範化以及把空格符的編碼"%20"替換為"+" . 按這套方法編碼的數據的MIME類型是application/x-www-form-urlencoded, 當前仍用於（雖然非常過時了）HTML與XForms規範中. 此外，CGI規範包括了web伺服器如何解碼這類數據、利用這類數據的內容。

> 如果發送的是HTTP GET請求, application/x-www-form-urlencoded數據包含在所請求URI的查詢成分中. 如果發送的是HTTP POST請求或通過email, 數據被放置在消息體中，媒體類型的名字被包含在消息的Content-Type頭內部。 


[URL encoding the space character: + or %20? - Stack Overflow](https://stackoverflow.com/questions/1634271/url-encoding-the-space-character-or-20)

[urlencode - Encoding URL query parameters in Java - Stack Overflow](https://stackoverflow.com/questions/5330104/encoding-url-query-parameters-in-java)

[你所不知道 ❌ URL - 小二の伤寒杂病论 - SegmentFault 思否](https://segmentfault.com/a/1190000006063123)
[你所不知道 ❌ PHP POST - 小二の伤寒杂病论 - SegmentFault 思否](https://segmentfault.com/a/1190000006043397)