---
title: CSP 網頁安全性設計
date: 2022-07-30 23:59:52
tags: [csp]
categories: 源碼掃描
---

最近把一個專案上到測試環境，明明網站是 http 環境，但是裡面網址竟然顯示 https，我查看原始碼也沒強制加 https，也沒有用`<base>`改變程式基本位置。

<!--more-->

## 發生原因

網頁吃不到CSS，用開發者工具看網路擷取到 JS、CSS都抓到404，發現導向內容為 HTTPS，程式碼都沒有做任何調整設定。

![](https://i.imgur.com/sgOYMF8.png)
![](https://i.imgur.com/Uzg118R.png)
![](https://i.imgur.com/IHGKdIw.png)
![](https://i.imgur.com/XNStivs.png)


## CSP upgrade-insecure-requests

```htmlembedded=
<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">
```

首先是這一段影響的，可以看[在https中使用http - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10185482)文章有講到，只要拔掉就能解決這個問題。目前我發現 localhost,127.0.0.1 不會強制導向 `https`，不過文章沒有找到相關說明。

假如網站要調 https，網站在不改後端情況下，可以 workaround 設定這個，所有連線都會走 https。

參考:
- [Content Security Policy (CSP) 筆記 - HackMD](https://hackmd.io/@Eotones/BkOX6u5kX)
- [在https中使用http - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10185482)
- [Upgrade-Insecure-Requests - HTTP | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Upgrade-Insecure-Requests)