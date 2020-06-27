---
title: 遇到Content-Length不正確到底發生什麼情況
date: 2019-04-05 23:50:06
tags: [nginx, apache]
categories: 寫程式一些事
---

最近公司有一個案子，對方用程式打 API 竟然會會帶`content-length:2`，但並未帶 body 值
導致程式會不正確，但對方說程式已經用現有的 function，所以沒辦法修正
最後這個問題還是沒法解決
這幾天研究`content-length:2`會影響什麼情形
在`公司`和`家`裡測試結果會不太一樣~~讓我矇 B~~
所以測試的時候不知道發生什麼事情
這邊只記錄我測出來結果

<!--more-->

注意這邊的 Content-Length 都先只 request header 裡面
不是只 response Content-Length

## 介紹 request Content-Length

簡單一句話，就是 POST DATA 資料傳給 Server 長度。

有找到更短詳細介紹

> - 響應為 1xx，204，304 相應或者 head 請求，則直接忽視掉消息實體內容。
> - 如果有 Transfer-Encoding，則優先採用 Transfer-Encoding 裡面的方法來找到對應的長度。比如說 Chunked 模式。
> - 「如果 head 中有 Content-Length，那麼這個 Content-Length 既表示實體長度，又表示傳輸長度。如果實體長度和傳輸長度不相等(比如說設置了 Transfer-Encoding)，那麼則不能設置 Content-Length。如果設置了 Transfer-Encoding，那麼 Content-Length 將被忽視」。這句話翻譯的優點饒，其實關鍵就一點：有了 Transfer-Encoding，則不能有 Content-Length。
> - 通過服務器關閉連接能確定消息的傳輸長度。(請求端不能通過關閉連接來指明請求消息體的結束，因為這樣可以讓服務器沒有機會繼續給予響應)。這種情況主要對應為短連接，即非 keep-alive 模式。
>   HTTP1.1 必須支持 chunk 模式。因為當不確定消息長度的時候，可以通過 chunk 機制來處理這種情況。
> - 在包含消息內容的 header 中，如果有 content-length 字段，那麼該字段對應的值必須完全和消息主題裡面的長度匹配。
>   其實後面幾條幾乎可以忽視，簡單總結後如下：
>   1、Content-Length 如果存在並且有效的話，則必須和消息內容的傳輸長度完全一致。(經過測試，如果過短則會截斷，過長則會導致超時。)
>   2、如果存在 Transfer-Encoding(重點是 chunked)，則在 header 中不能有 Content-Length，有也會被忽視。
>   3、如果採用短連接，則直接可以通過服務器關閉連接來確定消息的傳輸長度。(這個很容易懂)
>   結合 HTTP 協議其他的特點，比如說 Http1.1 之前的不支持 keep alive。那麼可以得出以下結論：
>   1、在 Http 1.0 及之前版本中，content-length 字段可有可無。
>   2、在 http1.1 及之後版本。如果是 keep alive，則 content-length 和 chunk 必然是二選一。若是非 keep alive，則和 http1.0 一樣。content-length 可有可無。

## 靜態資源

先講一下，這邊靜態資源是指`*.jpg,*.png,*js,*.json`
以下測試會以 nginx 為主

### HTTP Content-Length 數值不正確 可正常執行

連`http`時候，`靜態`資源不管`Content-Length`帶多少(正確或不正確)，apache,nginx 都可以正常回傳程式
我在我的電腦裝 nginx 測試
雖然有些網站可能不正常，目前推測可能有防火牆關係
防火牆可能會分析這封包不正確，可能會阻擋

不能正確連線 EX:

```
curl -v --request "GET" --header "content-length:2" http://www.cyut.edu.tw/~ywfan/index.htm
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying 163.17.1.15...
* TCP_NODELAY set
* Connected to www.cyut.edu.tw (163.17.1.15) port 80 (#0)
> GET /~ywfan/index.htm HTTP/1.1
> Host: www.cyut.edu.tw
> User-Agent: curl/7.58.0
> Accept: */*
> content-length:2
>
```

可以正常連線，這邊隨便有找到一個可正常連線
我上面先前我有打可能會跟防火牆(這邊防火牆不是指 OS 防火牆)
因為我家可以正常打、公司那邊會不正常
所以可能被硬體防火牆(Web Application Firewall)阻擋

家裡執行指令

```
 curl -v --request "GET" --header "content-length:200" http://jimmypm.ehosting.com.tw/a.htm
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying 203.69.42.72...
* TCP_NODELAY set
* Connected to jimmypm.ehosting.com.tw (203.69.42.72) port 80 (#0)
> GET /a.htm HTTP/1.1
> Host: jimmypm.ehosting.com.tw
> User-Agent: curl/7.58.0
> Accept: */*
> content-length:200
>
< HTTP/1.1 200 OK
< Content-Type: text/html
< Last-Modified: Thu, 04 Apr 2019 04:12:18 GMT
< Accept-Ranges: bytes
< ETag: "923231989cead41:0"
< Server: Microsoft-IIS/7.5
< X-Powered-By: ASP.NET
< Date: Fri, 05 Apr 2019 16:23:21 GMT
< Content-Length: 48739
<
```

公司執行指令結果

```
$  curl -v --request "GET" --header "content-length:200" http://jimmypm.ehosting.com.tw/a.htm
Note: Unnecessary use of -X or --request, GET is already inferred.
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0*  Trying 203.69.42.72...
* TCP_NODELAY set
* Connected to jimmypm.ehosting.com.tw (203.69.42.72) port 80 (#0)
> GET /a.htm HTTP/1.1
> Host: jimmypm.ehosting.com.tw
> User-Agent: curl/7.61.1
> Accept: */*
> content-length:200
>
  0     0    0     0    0     0      0      0 --:--:--  0:02:18 --:--:--     0*Empty reply from server

* Connection #0 to host jimmypm.ehosting.com.tw left intact
curl: (52) Empty reply from server
```

## 動態資源

例如`*.asp`,`*.jsp`,`*.php`
只要做到`Content-Length:2` 沒有送出`data`(POST DATA)
就會出錯，接收到`408` 連線逾時。

這邊簡單詳細講一下，Server 是主動關閉的，這時候 client 會持續等待，使用者關閉頁面，也不會影響結果
使用者沒有關閉的話，Server 沒接收 data 資料，就會回傳`408`
這時候使用者會接收到`408`

維基百科

> 408 Request Timeout
> 請求超時。根據 HTTP 規範，用戶端沒有在伺服器預備等待的時間內完成一個請求的傳送，用戶端可以隨時再次提交這一請求而無需進行任何更改

## HTTPS

不管靜態資源、動態資源都會錯誤

- 動態資源

```bash
curl -v --request "GET" --header "Content-Length:3" --data "12345"  https://tw.yahoo.com                                                                                       ✔  20  15:16:43
* Rebuilt URL to: https://tw.yahoo.com/
*   Trying 124.108.103.104...
* TCP_NODELAY set
* Connected to tw.yahoo.com (124.108.103.104) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: /etc/ssl/certs/ca-certificates.crt
  CApath: /etc/ssl/certs
* TLSv1.2 (OUT), TLS handshake, Client hello (1):
* TLSv1.2 (IN), TLS handshake, Server hello (2):
* TLSv1.2 (IN), TLS handshake, Certificate (11):
* TLSv1.2 (IN), TLS handshake, Server key exchange (12):
* TLSv1.2 (IN), TLS handshake, Server finished (14):
* TLSv1.2 (OUT), TLS handshake, Client key exchange (16):
* TLSv1.2 (OUT), TLS change cipher, Client hello (1):
* TLSv1.2 (OUT), TLS handshake, Finished (20):
* TLSv1.2 (IN), TLS handshake, Finished (20):
* SSL connection using TLSv1.2 / ECDHE-RSA-AES128-GCM-SHA256
* ALPN, server accepted to use h2
* Server certificate:
*  subject: C=US; ST=California; L=Sunnyvale; O=Oath Inc; CN=*.www.yahoo.com
*  start date: Feb  1 00:00:00 2019 GMT
*  expire date: Jul 31 12:00:00 2019 GMT
*  subjectAltName: host "tw.yahoo.com" matched cert's "tw.yahoo.com"
*  issuer: C=US; O=DigiCert Inc; OU=www.digicert.com; CN=DigiCert SHA2 High Assurance Server CA
*  SSL certificate verify ok.
* Using HTTP2, server supports multi-use
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* Using Stream ID: 1 (easy handle 0x55d96331c780)
> GET / HTTP/2
> Host: tw.yahoo.com
> User-Agent: curl/7.58.0
> Accept: */*
> Content-Length:3
> Content-Type: application/x-www-form-urlencoded
>
* We are completely uploaded and fine
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
* Connection #0 to host tw.yahoo.com left intact
curl: (16) Error in the HTTP2 framing layer
```

- 靜態資源

```bash
 curl  --request "GET" --header "Content-Length:3"  https://code.jquery.com/jquery-1.11.3.js
curl: (52) Empty reply from server
```

## Content-Length 接收的值>Header 值

我有簡單寫個 PHP 程式

```php
<?php
$data = file_get_contents('php://input');
var_dump($data);
```

簡單可以看出 post raw data
這邊用 nginx+phpfpm 測出來結果
其實會被截斷

```bash
curl -v --request "GET" --header "Content-Length:3" --data "12345"  http://192.168.1.121:32771/testpost.php
*   Trying 192.168.1.121...
* TCP_NODELAY set
* Connected to 192.168.1.121 (192.168.1.121) port 32771 (#0)
> GET /testpost.php HTTP/1.1
> Host: 192.168.1.121:32771
> User-Agent: curl/7.58.0
> Accept: */*
> Content-Length:3
> Content-Type: application/x-www-form-urlencoded
>
* upload completely sent off: 5 out of 5 bytes
< HTTP/1.1 200 OK
< Date: Sat, 06 Apr 2019 07:14:21 GMT
< Server: Apache/2.4.25 (Debian)
< Content-Length: 16
< Content-Type: text/html; charset=UTF-8
<
string(3) "123"
* Connection #0 to host 192.168.1.121 left intact
```

## Apache Cotent-Length 不會 408

今天發現有一個 PHP 網站，`content-length:x`都可以正常運作
不過發現是用`apache+mod_php`
我自己有測試還真的能 work

```bash
curl --request "GET" --header "Content-Length:3" http://mcu.no-ip.org/
```

可用這個 docker hub 做簡單測試
[eboraas/apache-php Dockerfile - Docker Hub](https://hub.docker.com/r/eboraas/apache-php/dockerfile)

PS:apache+php-fpm 不會回傳 response，但說奇怪 apache log 會顯示 200

以下是 apache+php-fpm

```bash
curl -v --request "GET" --header "Content-Length:3"  http://192.168.1.121:32772/testpost.php

Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying 192.168.1.121...
* TCP_NODELAY set
* Connected to 192.168.1.121 (192.168.1.121) port 32772 (#0)
> GET /phpinfo.php HTTP/1.1
> Host: 192.168.1.121:32772
> User-Agent: curl/7.58.0
> Accept: */*
> Content-Length:3
>
< HTTP/1.1 200 OK
< Date: Sat, 06 Apr 2019 07:08:31 GMT
< Server: Apache/2.4.7 (Ubuntu)
< Connection: close
<
* Closing connection 0
```

什麼 Apache 跟 nginx 結果不太一樣!!!
我也冏了!!!不過依然沒有找到相關文獻

在 Apache 官網文件[mod_policy - Apache HTTP Server Version 2.5](https://httpd.apache.org/docs/trunk/en/mod/mod_policy.html)

```
PolicyLength Directive
Description:	Enable the content length policy.
Syntax:	PolicyLength ignore|log|enforce
Default:	ignore
Context:	server config, virtual host, directory
Status:	Extension
Module:	mod_policy
Compatibility:	PolicyLength is only available in Apache 2.5.0 and later.
```

不確定是不是這個關係，目前他說這個選項 Apache2.5 才會有(現在才 Apache2.4)
我找不到 2.5 版本= =
有可能現在是讓他過

## Web Application Firewall 影響

有爬到這個，目前猜測公司或有些網站連不到可能之中有類似 WAF
這東西影響到
[Web Application Firewall 的偵測原理 – 軟體品管的專業思維](https://www.qa-knowhow.com/?p=4679)

> 6. HTTP 標準協議
>    檢查請求的內容是否符合 HTTP 標準協議, 這是一種比較有效且直覺的方式
>    任意修改 HTTP 協議內容可能會導致的攻擊如下:
>    http://capec.mitre.org/data/definitions/220.html
>    HTTP 協議中檢查的重點為:
>    MIME 中 filename 檔名
>    URL 合法性
>    Content-Length 數值
>    衝突與重複的 HTTP headers, 例如 keep-alive 與 close 重複與衝突
>    不安全的字元, 例如 ” “, “<“, “>”, “{“, “}”, “|”, “\”, “^”, “~”, “[“, “]”, and “`”.等
>    過濾異常缺少 Http header 的請求, 一般正常的請求會包含 Host, User-Agent, Accept headers, 因此如果缺少或是沒有值都是表示異常
>    Content-Length 不等於 0 但是卻沒有 Content-Type
>    HTTP protocol version

## nginx 從 phpfpm 那邊調整 改 header 失敗筆記

在這裡面[Nginx 之 fastcgi_param 参数详见 - 简书](https://www.jianshu.com/p/282db1543a78)
`fastcgi_param CONTENT_LENGTH $content_length; #请求头中的Content-length字段。`
發現這個不會正常執行
目前猜測 nginx 在接收 request 封包時候，第一時間不會做更改
但當時`Content-Length:2`會持續等待
所以可能在等接收資料，逾時會傳 408
所以根本~~不會做到修改 header 那一段~~正確來說 php-fpm 那段沒有執行到
收不到改 header 那段

## nginx POST 打靜態資源會 405

[Nginx 將靜態文件響應 POST 請求，提示 405 錯誤問題](https://gist.github.com/zxhfighter/7560812)

> Nginx 的 405 錯誤
> 絕大多數服務器，都不允許靜態文件響應 POST 請求(GET 請求靜態文件是天經地義的)，否則會返回 HTTP/1.1 405 Method not allowed 錯誤。
> 然而在前端開發中，前端開發工程師經常模擬後端請求，返回靜態數據來查看頁面效果，怎麼辦？

## 其他小記

curl 預設是 Content-Type: application/x-www-form-urlencoded ，假如要傳 raw data 要改成 Content-Type: text/plain

> curl's --data will by default send Content-Type: application/x-www-form-urlencoded in the request header. However, when using Postman's raw body mode, Postman sends Content-Type: text/plain in the request header.
> So to achieve the same thing as Postman, specify -H "Content-Type: text/plain" for curl:

重啟 docker nginx
[Docker: Reload Nginx Inside Container - ShellHacks](https://www.shellhacks.com/docker-reload-nginx-inside-container/)

```bash
docker container exec <container> nginx -t
docker container exec <container> nginx -s reload
```

Keep-Alive 为 close
也無法改變 nginx 的 content-length 問題
keepalive_timeout 0; ，就可以關掉

Full request/response body logging in nginx 沒有測試，好像要裝 lua-nginx-module
[Full request/response body logging in nginx](https://gist.github.com/morhekil/1ff0e902ed4de2adcb7a)

## 感想與結論

沒事**還是請傳好正確 content-length:0**
原本想讓 server 變成 http1.0
但好像怎麼用都失敗

對方說沒辦法改的話
最終大絕可能使用`apache+php_mod`不過最糟的情形還是會被 WAF 阻擋

這幾天總覺得浪費很多時間找這玩意
明明是對方的鍋，還是要想辦法解決
但是真的不希望遇到第二次 冏

**2020-05-27**

[用了这么久HTTP, 你是否了解Content-Length? | Fundebug博客 - 一行代码搞定BUG监控 - 网站错误监控|JS错误监控|资源加载错误|网络请求错误|小程序错误监控|Java异常监控|监控报警|Source Map|用户行为|可视化重现](https://blog.fundebug.com/2019/09/10/understand-http-content-length/)
這篇有簡單明確講到我這篇重點

其他參考來源:

- [Http Header 入門 | CCNP 筆記本](https://nkongkimo.wordpress.com/2010/04/28/http-header%E5%85%A5%E9%96%80/)
- [HTTP Request/Response 駭客跟我們想的不一樣 – 軟體品管的專業思維](https://www.qa-knowhow.com/?p=792)
- [security apache 與 php 關閉版本顯示 | SSORC.tw](https://ssorc.tw/1148)
- [資訊手札: Apache Http Header](http://chuehnoneinfonote.blogspot.com/2013/02/apache-http-header.html)
- [web-server | 平凡備忘錄](http://blog.gitdns.org/2016/06/23/web-server/)
- [HTTP 協議頭部與 Keep-Alive 模式詳解 - BYVoid](https://www.byvoid.com/zht/blog/http-keep-alive-header)
- [nginx 优化的一些建议 - 吕滔博客](https://lvtao.net/server/1458.html)
- [客户端 POST 错误，服务端应该回 200 还是 400？ - 知乎](https://www.zhihu.com/question/36007098)

- [Http 協議中關於 Content-Length 的解讀 - 簡書](https://www.jianshu.com/p/fa2e4e23aaaf)
- [Docker: Reload Nginx Inside Container - ShellHacks](https://www.shellhacks.com/docker-reload-nginx-inside-container/)
- [Nginx 设置 Keep-Alive 为 close - meteoric_cry - 博客园](https://www.cnblogs.com/meteoric_cry/archive/2011/06/07/2074150.html)
- [Nginx 將靜態文件響應 POST 請求，提示 405 錯誤問題](https://gist.github.com/zxhfighter/7560812)
- [Nginx 将静态文件响应 POST 请求，提示 405 错误问题 - 千思网](https://www.qiansw.com/nginx-responds-to-the-post-request-in-the-static-file-and-prompts-405-for-the-error-problem.html)
- [Full request/response body logging in nginx](https://gist.github.com/morhekil/1ff0e902ed4de2adcb7a)
- [Nginx 安装 lua-nginx-module 模块 - 拼搏的小船长 - CSDN 博客](https://blog.csdn.net/qq_25551295/article/details/51744815)
- [未連線: 潛在的安全性問題](https://notfalse.net/42/http-payload)
- [Web Application Firewall 的偵測原理 – 軟體品管的專業思維](https://www.qa-knowhow.com/?p=4679)
- [nginx 的 chunked_transfer_encoding 没有生效 - 开源中国](https://www.oschina.net/question/2547791_2150761)
- [5 Open Source Web Application Firewall for Better Security](https://geekflare.com/open-source-web-application-firewall/#1-ModSecurity)
- [不只是資安: [工具介紹] Apache 模組 mod_security - 以阻擋造訪路徑攻擊為例](http://cyrilwang.blogspot.com/2014/01/apache-modsecurity.html)
- [Nginx 之 fastcgi_param 参数详见 - 简书](https://www.jianshu.com/p/282db1543a78)
- [使用 curl 指令測試 REST 服務](http://blog.kent-chiu.com/2013/08/14/testing-rest-with-curl-command.html)
- [linux - How to post raw body data with curl? - Stack Overflow](https://stackoverflow.com/questions/43054195/how-to-post-raw-body-data-with-curl)
- [Where is php-fpm log file? Or how to activate it? Does PHP7 log errors? · Issue #48 · rlerdorf/php7dev](https://github.com/rlerdorf/php7dev/issues/48)
- [Nginx 啟用 PHP-FPM 服務狀態監控網頁教學 - G. T. Wang](https://blog.gtwang.org/linux/nginx-enable-php-fpm-status-page-tutorial/)
- [Where is the default zsh history file? - Stack Overflow](https://stackoverflow.com/questions/35190034/where-is-the-default-zsh-history-file)