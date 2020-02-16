---
title: wget 做 POST 表單發送
date: 2019-12-29 19:22:13
tags: [linux]
categories: Linux
---

最近收到一個工單
是有關於做大量資料做修改
目前有一隻程式做單一資料置換
要怎麼快速對大量資料直接用現有程式做呢?
那就多批發送表單吧

<!--more-->

Google 到 [使用curl和wget发送post请求_cws1214的专栏-CSDN博客](https://blog.csdn.net/cws1214/article/details/21440691) 可以簡單用 wget 使用
為什麼使用 wget 做呢? 這樣就可以留下 log 紀錄檔
相同的url 後面可以加?__idnum__
EX:`wget --post-data="user=user1&pass=pass1&submit=Login"  http://domain.com/path/page_need_login.php?12345`


接下來目錄下會有`page_need_login.php?12345`
這就可以當 log 資訊

快速解決!!


**2020-02-04**

今天接到一個需求，使用系統要大量資料做功能處理
但由於太多，頁面處理也需要很久的時間
於是這件工作變成 IT 工作需求

首先，這頁處理功能有確認帳號密碼
要先排除這個，需要找 wget 可以做 cookies 傳遞

[XYZ的筆記本: Linux 使用 curl (wget) 自動訪問網頁](https://xyz.cinc.biz/2014/10/linux-curl-wget-cookie.html)
[Export Cookies – 下載 🦊 Firefox 擴充套件（zh-TW）](https://addons.mozilla.org/zh-TW/firefox/addon/export-cookies-txt/)

抓取 cookies 方法
[wget 開啟 cookie](https://www.opencli.com/linux/wget-enable-cookie-download)
[wget取cookies 用 cookies @ echochio :: 痞客邦 ::](https://echochio.pixnet.net/blog/post/32574231-wget%E5%8F%96cookies-%E7%94%A8-cookies)

使用`wget -d --load-cookies=cookies "https://dc2.safesync.com/test.ova?dl=1"`
參考[[Mac] 使用 wget 送出 cookie，登入網站後下載檔案 | EPH 的程式日記](https://ephrain.net/mac-%E4%BD%BF%E7%94%A8-wget-%E9%80%81%E5%87%BA-cookie%EF%BC%8C%E7%99%BB%E5%85%A5%E7%B6%B2%E7%AB%99%E5%BE%8C%E4%B8%8B%E8%BC%89%E6%AA%94%E6%A1%88/)
可以 debug 出東西

但是我想直接用瀏覽器 Cookies 出來
一直在想 Cookies 要怎麼組
[Export Cookies – 下載 🦊 Firefox 擴充套件（zh-TW）](https://addons.mozilla.org/zh-TW/firefox/addon/export-cookies-txt/)
這個很快就解決問題

~~其實上述網頁就有寫格式，我當初沒注意到~~
有工具產生比較快

cookies 格式
```
dc2.safesync.com TRUE / FALSE 0 fmprm si
```

[XYZ的筆記本: Linux 使用 curl (wget) 自動訪問網頁](https://xyz.cinc.biz/2014/10/linux-curl-wget-cookie.html)

```


取cookies
wget --cookies=on --keep-session-cookies --save-cookies=cookie.txt "http://server.com/" -O 123.html

用cookies
wget --referer="http://server.com/" --cookies=on --load-cookies=cookie.txt --keep-session-cookies --save-cookies=cookie.txt "http://server.com/menu" -O 456.html

取cookies 加 user & pass
wget --cookies=on --save-cookies=cookies.txt --post-data 'user=chio&password=chio' "http://server.com/auth.php"

用cookies
wget --cookies=on --load-cookies=cookies.txt -m -E -k "http://server.com/interesting/article.php"

```

[wget取cookies 用 cookies @ echochio :: 痞客邦 ::](https://echochio.pixnet.net/blog/post/32574231-wget%E5%8F%96cookies-%E7%94%A8-cookies)

相信透過這些很簡單可以寫個自動化工具!!

## timeout try 次數

[linux - Does WGET timeout? - Stack Overflow](https://stackoverflow.com/questions/2291524/does-wget-timeout)

預設 900 秒  嘗試次數 5 次