---
title: CORS實作可能要注意的事情
date: 2019-03-07 20:47:01
tags: [cors, ajax]
categories: JavaScript
---

雖然平常沒有使用 CORS
但是之前有嘗試了解 CORS 流程原理
其實瀏覽器會先送 OPTIONS 封包做確認
有做跨域 header 東西，就會傳送資訊
今天突然想到傳送兩次
不是會做兩次程式東西
我稍微爬了一下文章
原來我想的問題真的是有這個問題

<!--more-->

看到這篇[CORS 跨域原理浅析 - 知乎](https://zhuanlan.zhihu.com/p/29980092) {% asset_link web.png 備份圖 %}
裡面有提到很多很細的內容
我自己也有用簡單 PHP 測試

```php
<?php
header("Access-Control-Allow-Origin: *");
header("Access-Control-Allow-Headers: *");
echo 'work!' . PHP_EOL;
$fp = fopen('test.txt','a+');
fwrite($fp,'1'. PHP_EOL);
fclose($fp);
echo 'finial' . PHP_EOL;
```

簡單用`php -s 0.0.0.0:8888`就可以啟動 php 簡易 server
在隨便網頁開啟開發者工具，可以`fetch('http://localhost:8888/test.php')`
可以看`test.txt`有幾個 1
就知道執行幾次

雖然平常沒甚麼機會使用 CORS，但是還是紀錄這個容易忽略的事情
話說 Laravel 好像也要注意怎麼修改

相關連結:

- [輕鬆理解 Ajax 與跨來源請求 | TechBridge 技術共筆部落格](https://blog.techbridge.cc/2017/05/20/api-ajax-cors-and-jsonp/)
- [CORS 跨域原理浅析 - 知乎](https://zhuanlan.zhihu.com/p/29980092)
- [Laravel 处理 OPTIONS 请求的原理探索及  批量处理方案 - 知乎](https://zhuanlan.zhihu.com/p/33542992)
- [PHP, Larave, Nginx 設置 Header 允許請求 CORS 的幾種方式 « 關於網路那些事...](https://adon988.logdown.com/posts/7816040-cross-domain-cros-request-issues)
- [✋🏼🔥 CS Visualized: CORS - DEV Community](https://dev.to/lydiahallie/cs-visualized-cors-5b8h)