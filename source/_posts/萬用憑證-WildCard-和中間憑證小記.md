---
title: 萬用憑證(WildCard)和中間憑證小記
date: 2018-10-10 23:54:38
tags: [ssl,tls,wildcard,憑證]
categories: 程式心法
---

最近公司https網站很奇怪，桌面版的Chrome,Firefox顯示正常(安全)
但是手機版的Chrome正常，Firefox不正常(非安全)
倒置Line App內崁網頁不正常(連結https那台主機沒有正常跳頁)

<!--more-->

之前在保哥粉絲團看過[申請 Let's Encrypt 免費 SSL 憑證一次就上手](https://www.facebook.com/will.fans/videos/1760703873958749/)
所以對憑證有基本的了解
但沒深入了解

目前有些網站憑證，手機版(Android)Webview會有問題。IOS皆正常
但點入登入(網域)LINE會空白頁&Android Firefox會顯是為安全憑證

正常桌面瀏覽器

{% asset_img a.png a網域 %}
{% asset_img b.png b網域 %}

{% asset_img android_fierfox.png android_firefox %}
{% asset_img line_webview.png line_webview %}


發現公司不同子網域是用`萬用憑證`
簡單來說就是`*.xxxx.com.tw`
但問題來了...，為什麼a網段根b網段(都是*.xxxx.com.tw)
為什麼a正常、b不正常

最後爬到這個，[DigiCert 的证书，在浏览器（firefox，chrome）上都绿了应该没问题，但是 wget 总是出错 - V2EX](https://www.v2ex.com/t/138734)
intermediate certifcate(中間憑證)有問題...
瀏覽器會智能判斷，所以不會跳出錯誤
經過實驗postman也不會出錯....(可能他是Chrome吧XD)

{% asset_img curl_ok.png a網域可以連 %}
{% asset_img curl_no_ok.png b網域不能連 %}

感覺哪裡怪怪的
後來發現[SSL Certificate Checker - Diagnostic Tool | DigiCert.com](https://www.digicert.com/help/)可以測試憑證


{% asset_img a_digicert.png a_digicert檢查 %}
{% asset_img b_digicert.png b_digicert檢查 %}


{% asset_img yahoo_digicert.png 正常yahoo的 %}


[你可能不知道的憑證大小事 - 吾輩乃程序猿](https://lv5.in/2016/11/22/%E4%BD%A0%E5%8F%AF%E8%83%BD%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84%E6%86%91%E8%AD%89%E5%A4%A7%E5%B0%8F%E4%BA%8B/)
[[筆記] SSL 憑證購買記 - Yu-Cheng Chuang’s Blog](https://blog.yorkxin.org/2009/02/23/buying-a-turbo-ssl-cert.html)
裡面有介紹中繼憑證可能會影像憑證安全性

跟MIS說是憑證問題，可能要查看看
不過MIS說是廠商給他的，所以可能他要問廠商才清楚
目前查到這樣了

其他相關筆記
* [[Web] 注意 Wildcard 憑證的適用保護範圍 | EPH 的程式日記](https://ephrain.net/web-%E6%B3%A8%E6%84%8F-wildcard-%E6%86%91%E8%AD%89%E7%9A%84%E9%81%A9%E7%94%A8%E4%BF%9D%E8%AD%B7%E7%AF%84%E5%9C%8D/)
* [如果您的用戶在Chrome與其他瀏覽器中看到警告資訊，那麼您需要做什麼 | DigiCert & Symantec](https://www.websecurity.symantec.com/zh/tw/blog/what-to-do-if-you-see-warning-messages-in-your-browser)
