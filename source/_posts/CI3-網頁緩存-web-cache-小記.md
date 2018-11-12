---
title: CI3 網頁緩存(web cache)小記
date: 2018-10-13 18:39:39
tags: [CI]
categories: [Codeigiter]
---

最近做一個排行榜API
但回傳時間真的太久了
於是做一個緩存
突然想到Codeigiter內建有網頁緩存裝置
竟然內建有這個功能
那還要自己手刻嗎XD

<!--more-->

PHP
```php
		if(! isset($_SERVER['HTTP_X_KEY']) ||  $_SERVER['HTTP_X_KEY'] != "xxxxxxx")
        {
            http_response_code( 500 );
            die( 'NO ENTER' );
        }
        header("x-api-key: xxxxxxx");
		$this->output->cache(1);
		echo "hello world2";
```

就這麼簡單!!!

原本怕這邊簡單測試假如失敗的時候，會不會直接緩存
會影響OK的，
`測試結果是不會的`

cache(`分鐘`)

最後小記

因為PHP Codeigiter是吃a權限(不是apache)
但是cache是資料夾是設定755
原本想先對cache做777
其實我還不知道資料夾權限是吃什麼
最後可以跑cache

所以最後看到cache檔案室吃`apache`
其實把那個資料夾改成`apache`權限就能用了
也沒必要設定777

假如真的覺得他的cache檔案設定不太好
可以從設定檔設定放置路徑
```
$config['cache_path'] = ""。如果您不想使用默認的緩存目錄（system/cache/）來存儲緩存，可以設置完整的服務器目錄  
```
[【ci框架】config.php配置文檔解讀 - 掃文資訊](https://hk.saowen.com/a/2247a2730eb875d73a771b369fa169f2ec85e0a7766e7b9d69fe5cc1fded6849)


有空來做php 讀取封包header筆記
* [PHP 讀取客製化傳送進來的 HTTP Header | Tsung's Blog](https://blog.longwin.com.tw/2017/10/php-get-custom-http-header-2017/)
* [PHP無法在$_SERVER內取得自訂header](https://kingfff.blogspot.com/2017/05/apache-php-header-server-invalid-characters-underscores.html)

`apache_request_headers()`好像要裝apache套件才能用的樣子
[PHP: getallheaders - Manual](http://php.net/manual/en/function.getallheaders.php)

所以最後只能用原生PHP抓header方法
參照[PHP無法在$_SERVER內取得自訂header](https://kingfff.blogspot.com/2017/05/apache-php-header-server-invalid-characters-underscores.html)
順利解決