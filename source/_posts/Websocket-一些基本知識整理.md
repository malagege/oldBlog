---
title: Websocket 一些基本知識整理
date: 2022-02-13 22:50:17
tags: [javascript,websocket]
categories: JavaScript
---

## 我的錯誤印象(應該沒有人要看，可跳過)

在看 websocket 之前，我以為 websocket 是在 HTTP 協定，因為跟 EventSource(server sent events)很像，只是是做單方面傳輸，又簡單看相關文章，EventSource會有Proxy問題，但是websocket 不會有問題，websocket 有 SLB問題。在文章都有提到 HTTP header 相關，開發工具也可以看到相關資訊，以為傳輸資料就是一般request/response，但最近時實作發現我錯了。

<!--more-->

## 非http協定

在傳輸前會跟 http 做溝通，才會傳輸 websocket ，但是並不是傳統的request/response。開發工具只換看到一筆(ws)，但是裡面內容

![](https://www.ruanyifeng.com/blogimg/asset/2017/bg2017051503.jpg)

> 其他特點包括：
> （1）建立在 TCP 協議之上，服務器端的實現比較容易。
> （2）與 HTTP 協議有著良好的兼容性。默認端口也是80和443，並且握手階段採用 HTTP 協議，因此握手時不容易屏蔽，能通過各種 HTTP 代理服務器。
> （3）數據格式比較輕量，性能開銷小，通信高效。
> （4）可以發送文本，也可以發送二進制數據。
> （5）沒有同源限制，客戶端可以與任意服務器通信。
> （6）協議標識符是ws（如果加密，則為wss），服務器網址就是 URL。

詳細: [WebSocket 教程 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2017/05/websocket.html)

## 封包一些事情

![](https://i.imgur.com/2qJxY4X.png)
![](https://i.imgur.com/IoL3MTC.png)



> Websocket 是一個基於 TCP 的傳輸協議，主要都應用在瀏覽器與 server 雙向溝通，具有靈活及高效能。
> 
> ws 表示基於TCP的 Websocket，預設使用 80埠。 wss 則表示執行在 TLS 之上的 Websocket，預設使用 443埠。
> 
> HTTP協議溝通過程都需要攜帶 Head，在傳統網站通訊都是透過輪巡來向 server 發出請求，容易造成 server端的負擔。
> 
> Websocket 在首次建立連結是透過 HTTP GET 發出請求，與 server 取得溝通後，接續就會使用websocket協議來進行溝通。 未攜帶數據情況下，client 僅需要少量的掩碼(4字節)，server 端則只需要 2-10 Byte。相較於 polling 與 log-polling ，websocket 可以在更短的時間傳送更多資料。
> 
> Websocket 通訊傳輸層次
> 在 OSI 模型中，Http 與 Websocket (FTP, SMTP, POP3..)都屬於第七層 應用層，基礎是建立於 TCP 協議之上。
> 
> 此外，Websocket 在前面提過，首次握手是透過HTTP來達成。 後續才是用 Websocket 雙向溝通。
> 
> Socket 與 Websocket 之間的關係
> Socket 是在應用層和傳輸層之間的一個抽象層，原則上，Socket並非協議方式存在，主要是用於搭起 TCP/IP 與UDP/IP 或 ICMP 的連接服務，當兩個服務進行通訊時，首先就須要透過 socket 連線，例如， TCP 或 UDP 就是透過 socket 先建立起連線，在這連線過程會依照協議來做溝通。 所以，Socket 屬於傳輸控制層的抽象層，Websocket 則屬於應用層的協議。
> 心跳機制保持 Websocket 連線
> 在 Websocket 建立後，服務其實相當脆弱，只要遇到連線問題，客戶端就會發送關閉連線的 Fin，很容易就會造成斷開，造成服務中斷。有時，也可能是服務端有發生異常，但客戶端仍持續連線，或者是客戶端異常，但server端仍以為客戶端存在，造成zonbit connection。因此，會透過心跳機制來維持雙向通訊，能持續提供服務。 基本機制是由兩方透過 ping, pong 的接收與回應，持續確認連線正常。比較推薦的通常是在客戶端定期發送心跳，讓 server 進行監聽，當客戶端經過一段時間沒有發送訊息，server端就會自動斷開。關於心跳機制部分，可參考 - 即時通訊架構-心跳檢測原理說明。


以上參考[深入理解 Websocket 協議，常見攻擊手法及防護策略](https://hoohoo.top/blog/gain-an-in-depth-understanding-of-websocket-protocols-common-attack-techniques-and-protection-strategies/)，更多資訊可以看到裡面。


## 沒有實作廣播(broadcast)

在沒深入websocket文章時候，看相關文章都有做廣播功能，因為那時候有自學 RabbitMQ，看這種功能類似 MQ 概念，但最近用JAVA時做 websocket 簡單範例，發現顛覆我的印象。所以websocket

[WebSocket Test Client - Chrome 線上應用程式商店](https://chrome.google.com/webstore/detail/websocket-test-client/fgponpodhbmadfljofbimhhlengambbn)

## SLB 解決

發現 websocket 不是純 http，但他是應用層，所以我猜測她還是有機會用 sticky session 機制繞過去。有看到說明ws 相容 http ，所以應該可以做到?簡單docker-compose scale 看看。

- [nodejs執行環境 · Docker學習筆記](https://peihsinsu.gitbooks.io/docker-note-book/content/nodejs-runtime.html)
- [JavaScript | WebSocket 讓前後端沒有距離. WebSocket 是網路協定的一種， Client 可以透過此協定與… | by 神Q超人 | Enjoy life enjoy coding | Medium](https://medium.com/enjoy-life-enjoy-coding/javascript-websocket-%E8%AE%93%E5%89%8D%E5%BE%8C%E7%AB%AF%E6%B2%92%E6%9C%89%E8%B7%9D%E9%9B%A2-34536c333e1b)



## 登入驗證(Oauth)

## 測試工具

[curl - How to read websocket response in linux shell - Stack Overflow](https://stackoverflow.com/questions/47860689/how-to-read-websocket-response-in-linux-shell)

[Test a WebSocket using curl. · GitHub](https://gist.github.com/htp/fbce19069187ec1cc486b594104f01d0)

[Sending Message To Specific Anonymous User On Spring WebSocket - Michael Hoffmann (Mokkapps) - Senior Frontend Developer (Freelancer)](https://mokkapps.de/blog/sending-message-to-specific-anonymous-user-on-spring-websocket/)

## 其他文章


[WebSocket 的故事（二）—— Spring 中如何利用 STOMP 快速构建 WebSocket 广播式消息模式 - 掘金](https://juejin.cn/post/6844903655477346317)，有講到STOMP

## 程式實作

[PlayingWithWebsocket/EchoServer.java at master · sharif2008/PlayingWithWebsocket · GitHub](https://github.com/sharif2008/PlayingWithWebsocket/blob/master/src/java/com/websocket/test/EchoServer.java)

有實作廣播(broadcast)功能，簡單程式可以參考看看。


[webSocket+jwt实现方式 - 我是属车的 - 博客园](https://www.cnblogs.com/asker009/p/13507877.html) 

[springboot整合WebSocket | 分享知识](https://www.tapme.top/blog/detail/2018-08-25-10-38/)


