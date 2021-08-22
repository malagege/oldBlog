---
title: Chrome 莫名其妙 localhost 會導到 HTTPS
date: 2021-08-22 15:43:28
tags: [chrome,https,localhost,htst]
categories: WEb
---

上次在本機裝 mkcert 搭配使用 vite，[Vite 使用 https 憑證服務方法(搭 mkcert ) | 程式狂想筆記](https://malagege.github.io/blog/2021/07/04/Vite-%E4%BD%BF%E7%94%A8-https-%E6%86%91%E8%AD%89%E6%9C%8D%E5%8B%99%E6%96%B9%E6%B3%95-%E6%90%AD-mkcert/)，有做到架起 localhost HTTPS 服務，但後來 Vite 關閉 https 發現還是會導頁至 https，後來我就直接開無痕視窗解決這個問題。今天受不了，網路上找答案。

<!--more-->

## 問題

![](https://i.imgur.com/eTggfqk.png)


疑似 HTST 關係，所以頁面會導 HTTPS。



## 解決方式

參考方式:
- [瀏覽器強制跳轉 HTTPS 網址怎麼辦？ | Welcome.Web.World](https://hsiangfeng.github.io/other/20200723/3866554212/)
- [https chrome 打不開 解決 localhost 的 http 網站被強制導向 https 的問題 edge適用 (hsts) - HackMD](https://hackmd.io/@Not/H1zDgerN8)
- [Chrome] HTTP 網站一直被重新導向至 HTTPS 網站？ | EPH 的程式日記](https://ephrain.net/chrome-http-%E7%B6%B2%E7%AB%99%E4%B8%80%E7%9B%B4%E8%A2%AB%E9%87%8D%E6%96%B0%E5%B0%8E%E5%90%91%E8%87%B3-https-%E7%B6%B2%E7%AB%99%EF%BC%9F/)


1. Chrome 網址輸入 `chrome://net-internals/#hsts`，可以查看`domain`訊息。也可以刪掉。

![](https://i.imgur.com/UYW2UK2.png)


刪除後就正常了 :D。