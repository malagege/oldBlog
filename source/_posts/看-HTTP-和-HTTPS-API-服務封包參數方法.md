---
title: 看 HTTP 和 HTTPS API 服務封包參數方法
date: 2021-06-27 14:46:39
tags: [http,https,api,request]
categories: 實用工具
---


最近程式測試API，有時候好奇程式傳出去內容是否跟 Postman 一樣，但是 DEBUG 時候真的不方便。最近在想有什麼方法(或工具)可以做到?
之前有看到有用 `SSLKEYLOGFILE` 可以用 Wireshark 解封包。[深入理解 TLS/SSL 安全加密協定](https://www.facebook.com/119279178101235/videos/374050803968418)
雖然 Java 也有看到類似方法，但是要特別設定 [jSSLKeyLog](https://jsslkeylog.github.io/)，還要安裝 Wireshark 軟體，所以覺得很麻煩?
最近看有沒有比較簡單方式測試?稍微爬一下文整理

[HTTP test server accepting GET/POST requests - Stack Overflow](https://stackoverflow.com/questions/5725430/http-test-server-accepting-get-post-requests/)


裡面看到 socat 大法最方便，可看封包又能轉傳！

<!--more-->

## Httpbin

[httpbin.org](https://httpbin.org/)

```shell=
docker run -p 80:80 kennethreitz/httpbin
```

但這點在程式直覺看結果還是很不方便

## Post Test Server V2


[PTS - V2](http://ptsv2.com/)

有 http , https 方式接收資料，還算不錯。但沒有自己架設版本

## nc

推薦使用這個指令，簡單好用。不知道 socat 也能不能做到這種事情，還能順便轉送出去封包?XD(下面有實作出來)
最大缺點應該就是不能 https 

```shell=
nc -kdl localhost 8000

```

## socat 

**超級**推薦使用這個指令，可看到資料，又可以推送出去，不是很棒嗎!!連 https 都能搞定

```shell=
socat -v TCP-LISTEN:8080,fork,reuseaddr TCP:tw.yahoo.com:80
```

```shell=
# http to https
socat -v TCP-LISTEN:8080,fork,reuseaddr OPENSSL:web_url:443,verify=0
# 測試用
curl -H Host:web_url http://localhost:8080/
```

這邊加 `verify=0` 是不能檢查憑證，這樣可以用瀏覽器看，但網站有做 Host 驗證的話，可以設定瀏覽器 Header設定 Host ，找[ModHeader - Chrome 線上應用程式商店](https://chrome.google.com/webstore/detail/modheader/idgpnmonknjnojddfkpgkljpfnnfcklj/related)就能做到，不然就是改 Host檔案。這個是參考 Traefik 那篇([在 Raspberry PI 架設 Traefik 初體驗 | 程式狂想筆記](https://malagege.github.io/blog/2020/10/13/%E5%9C%A8-Raspberry-PI-%E6%9E%B6%E8%A8%AD-Traefik-%E5%88%9D%E9%AB%94%E9%A9%97/#%E6%B8%AC%E8%A9%A6%E6%96%B9%E6%B3%95))

相對，API 程式要加 Header，不然就是設定 Host 檔案，目前沒有找到 socat 修改 Header 方法。

參考來源: [SSH over SSL, episode 2: replacing proxytunnel with socat](https://blog.chmd.fr/ssh-over-ssl-episode-2-replacing-proxytunnel-with-socat.html)

## webhook.site

[Webhook.site - Test, process and transform emails and HTTP requests](https://webhook.site/)

之前 line webhook 時候有用這個測試，非常好用。缺點只能用 https ...，這算缺點嗎?(XD)
我要用這個測試 Java 8 憑證不認得這個網站憑證...，官方也有`docker` 自架服務，不過我就沒有嘗試。


## PutsReq 

[PutsReq](https://putsreq.com/)

也是有不錯操作，但沒有自架服務。



## 其他爬到東西

[vi/websocat: Command-line client for WebSockets, like netcat (or curl) for ws:// with advanced socat-like functions](https://github.com/vi/websocat)
