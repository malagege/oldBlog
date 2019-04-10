---
title: "$_POST和php://input小記"
date: 2019-04-08 21:03:00
tags: [php]
categories: PHP
---

最近有一支 API 踩到一個 API
他 request header 帶`Content-Type: json`
但我們平常寫程式可能不會注意到的地方
這邊我小記一下

<!--more-->

## \$\_POST

**只有 application/x-www-form-urlencoded or multipart/form-data 是有效的!!**
**只有 application/x-www-form-urlencoded or multipart/form-data 是有效的!!**
**只有 application/x-www-form-urlencoded or multipart/form-data 是有效的!!**

所以當`Content-Type:application/json`
\$\_POST 變數不會有任何作用!!!

> An associative array of variables passed to the current script via the HTTP POST method when using application/x-www-form-urlencoded or multipart/form-data as the HTTP Content-Type in the request.
> $HTTP_POST_VARS contains the same initial information, but is not a superglobal. (Note that $HTTP_POST_VARS and \$\_POST are different variables and that PHP handles them as such)

> 當 HTTP POST 請求的 Content-Type 是 application/x-www-form-urlencoded 或 multipart/form-data 時，會將變量以關聯數組形式傳入當前腳本。
> $HTTP_POST_VARS 包含相同的信息，但它不是一個超全局變量。 (注意 $HTTP_POST_VARS 和 \$\_POST 是不同的變量，PHP 處理它們的方式不同)

EX:

## php://input

**Content-Type:multipart/form-data 是無效的!!**
**Content-Type:multipart/form-data 是無效的!!**
**Content-Type:multipart/form-data 是無效的!!**

> php://input 是個可以訪問請求的原始數據的只讀流。 POST 請求的情況下，最好使用 php://input 來代替 $HTTP_RAW_POST_DATA，因為它不依賴於特定的 php.ini 指令。 而且，這樣的情況下 $HTTP_RAW_POST_DATA 默認沒有填充， 比激活 always_populate_raw_post_data 潛在需要更少的內存。 enctype="multipart/form-data" 的時候 php://input 是無效的。

仔細想想，可以開發者工具來看，通常用**multipart/form-data**都是用來上傳檔案的，所以會看到一些串流內容

```
之前看過Xuite API，[Xuite 開發者首頁](http://api.xuite.net/document/xuite_dev/public/front/index/id/267)
他們上傳檔案API是要打兩次
第一次取upload資訊，會取得第二次上傳打API網址
```

事後覺得我想太多，他們 Content-Type 不是傳 JSON

[PHP: 支持的协议和封装协议 - Manual](https://www.php.net/manual/zh/wrappers.php)

[XYZ 的筆記本: PHP 使用 file_get_contents 接收 POST 的資料](https://xyz.cinc.biz/2013/06/php-filegetcontents-post.html) {% asset_link 1.png 備份圖 %}
[深入剖析 PHP 输入流 php://input -- 简明现代魔法](http://www.nowamagic.net/academy/detail/12220520) {% asset_link 2.png 備份圖 %}

## \$HTTP_RAW_POST_DATA

PHP7.0 已移除，建議不要使用。

**This feature was DEPRECATED in PHP 5.6.0, and REMOVED as of PHP 7.0.0.**

> $HTTP_RAW_POST_DATA contains the raw POST data. See always_populate_raw_post_data.
> In general, php://input should be used instead of $HTTP_RAW_POST_DATA.
