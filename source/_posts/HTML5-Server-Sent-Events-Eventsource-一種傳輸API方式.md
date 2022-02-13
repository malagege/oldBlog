---
title: HTML5 Server-Sent Events(Eventsource) 一種傳輸API方式
date: 2022-02-13 23:21:58
tags: [javascript,eventsource,html5]
categories: JavaScript
---
以前看這個沒有仔細看，以為就是瘋狂 AJAX 動作，很多網站沒教後端怎麼處理，最近看 API 書籍，裡面有講到 HTTP Streaming 東西(Server-Sent Events)，突然意識到這個機制是不一樣，但這個機制沒有普及，也有不少缺點，如Proxy代理緩存問題，所以很少看到網站使用這個。這邊就簡單紀錄。

<!--more-->


## 流程

簡單來說就是連接那個檔案，網路會讓瀏覽器一直接收內容，不會關閉連線。所以假如Server沒做連線關閉，使用者直接下載檔案可以做下載動作。

{% mermaid sequenceDiagram %}
    Client->>Server: Open event streaming
    Server->>Client: event
    Client->>Client: onmessage
    Server->>Client: event
    Client->>Client: onmessage
    Server->>Client: event
    Client->>Client: onmessage
{% endmermaid %}


其他實作可參考:
[HTML5 Server-Sent Events(伺服器發送事件) 教學範例 for PHP - MIS 腳印](https://www.footmark.info/programming-language/javascript/html5-server-sent-events-php/)
[Server-Sent Events 教程 - 阮一峰的网络日志](https://www.ruanyifeng.com/blog/2017/05/server-sent_events.html)
[Spring Boot使用Server-Sent Events - 知乎](https://zhuanlan.zhihu.com/p/60227266)
[Server-Sent Events入门 - strongmore - 博客园](https://www.cnblogs.com/strongmore/p/15056942.html)
[非同步 Server-Sent Event](https://openhome.cc/Gossip/ServletJSP/ServerSentEvent.html)