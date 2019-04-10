---
title: nginx設定小記
date: 2019-04-07 19:11:33
tags:
categories:
---

最近專案開始有使用 Nginx
之前沒有什麼機會用到
所以對設定也不是很清楚
趁這個機會整理

<!--more-->

這邊有爬到[nginxconfig.io](https://nginxconfig.io/)
可以簡單設定 nginx 設定
這篇主要是紀錄目前專案有用到的設定

先分享最近專案用的 Codeigniter 的 nginx 設定檔

```
server {
    listen 80;
    index index.php index.html;
    server_name localhost;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /usr/local/nginx/html;

    location / {
        try_files $uri $uri/ /index.php;
    }

    location ~  ^.+\.php {
        fastcgi_pass   php-fpm:9000;
        fastcgi_index  index.php;
        fastcgi_split_path_info ^(.+\.php)(.*)$;
        fastcgi_param   PATH_INFO $fastcgi_path_info;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        fastcgi_param    PATH_TRANSLATED    $document_root$fastcgi_path_info;
        include        fastcgi_params;
    }

    # unless the request is for a valid file (image, js, css, etc.), send to bootstrap
    if (!-e $request_filename)
    {
        rewrite ^/(.*)$ /index.php?/$1 last;
        break;
    }
}
```

最近專案使用[Codeigniter 在 nginx 上設定 rewrite 的方法 @ 秋風落葉、萍水相逢 - Matt 網頁製作 生活旅遊 日誌 :: 痞客邦 ::](http://matt7137.pixnet.net/blog/post/42754388-codeigniter%E5%9C%A8nginx%E4%B8%8A%E8%A8%AD%E5%AE%9Arewrite%E7%9A%84%E6%96%B9%E6%B3%95)

## location

> 語法規則
> location [ = | ~ | ~* | ^~ ] uri { ... }
> location @name { ... }
> 語法規則很簡單，一個 location 關鍵字，後面跟著可選的修飾符，後面是要匹配的字符，花括號中是要執行的操作。
> 修飾符
> = 表示精確匹配。只有請求的 url 路徑與後面的字符串完全相等時，才會命中。
> ~ 表示該規則是使用正則定義的，區分大小寫。
> ~\* 表示該規則是使用正則定義的，不區分大小寫。
> ^~ 表示如果該符號後面的字符是最佳匹配，採用該規則，不再進行後續的查找。

參考來源
[一文弄懂 Nginx 的 location 匹配 - 個人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000013267839) {% asset_link location_1.png 備份圖 %}

> location 順序/優先級：
> (location =) > (location 完整路徑) > (location ^~ 路徑) > (location ~,~\* 正則順序) > (location 部分起始路徑) > (/)

參考來源
[nginx 中 location 的顺序(优先级)及 rewrite 规则写法 - 永福的博客 - 开源中国](https://my.oschina.net/foreverich/blog/800359)

## root

簡單來說就是放的程式路徑

> root 是指定項目的根目錄，適用與 server 和 location。可以指定多個，如果 locaiton 沒有指定，會往其外層的 server 或 http 中尋找繼承。

更多內容可以看[Nginx 靜態服務配置---詳解 root 和 alias 指令 - 簡書](https://www.jianshu.com/p/4be0d5882ec5) {% asset_link root_1.png 備份圖 %}

## try_files

這個函示有些 rewrite 可以套用在這功能上面
簡單檔案不存在的時候，可以自己設定指定到某一隻檔案
但要注意那個檔案需要存在，否則會 ERROR

看到網路上有人拿來做 cache

```
        location / {
                # 檢查是否有這個檔案或資料夾存在，不存在則使用index.php內容
                try_files $uri $uri/ /index.php;
        }
```

`rewrite ^/(.*)$ /index.php?/$1 last;`這段寫法是在網址前面加上 index.php
只要有關 index.php 都會導入到 phpfpm 裡面去

[Nginx 的 try_files 参数保证能懂的讲解-10536390-51CTO 博客](https://blog.51cto.com/10546390/1754757) {% asset_link 2.png 備份圖 %}
[Nginx 的 try_files 指令使用实例 - 运维之美](https://www.hi-linux.com/posts/53878.html)
[请给你的 Laravel 一份更好的 Nginx 配置 · yii.im](https://yii.im/posts/the-right-way-to-set-nginx-for-laravel/) {% asset_link 1.png 備份圖 %}

## nginx if

> 計算指定的 condition 的值。如果為真，執行定義在大括號中的 rewrite 模塊指令，並將 if 指令中的配置指定給請求。if 指令會從上一層配置中繼承配置。
> 條件可以是下列任意一種：
> 變量名；如果變量值為空或者是以「0」開始的字符串，則條件為假；
> 使用「=」和「!=」運算符比較變量和字符串；
> 使用「~」（大小寫敏感）和「~\*」（大小寫不敏感）運算符匹配變量和正則表達式。正則表達式可以包含匹配組，匹配結果後續可以使用變量$1..$9 引用。如果正則表達式中包含字符「}」或者「;」，整個表達式應該被包含在單引號或雙引號的引用中。
> 使用「-f」和「!-f」運算符檢查文件是否存在；
> 使用「-d」和「!-d」運算符檢查目錄是否存在；
> 使用「-e」和「!-e」運算符檢查文件、目錄或符號鏈接是否存在；
> 使用「-x」和「!-x」運算符檢查可執行文件；

[nginx location if 的匹配規則-殘劍-51CTO 博客](https://blog.51cto.com/cuidehua/1874770)
[Nginx 配置陷阱之万恶的 if 指令 – 虚伪的灵魂](https://xwsoul.com/posts/761)
[XYZ 的筆記本: Nginx if、and、網域名稱、GET 變數判斷](https://xyz.cinc.biz/2016/06/nginx-if-and-host-get-variable.html)

## rewrite

> Context: server, location, if
> rewrite regex replacement [flag];

[搞懂 nginx 的 rewrite 模块 - youyu 岁月 - SegmentFault 思否](https://segmentfault.com/a/1190000008102599)
[第七章：nginx 的 rewrite 规则详解 - 简书](https://www.jianshu.com/p/3b2345f7347d)
[Nginx Rewrite 規則寫法 | Helloyungke](https://yungke.me/nginx-location-rewrite/)

因為我的專案程式有掛 nginx docker 裡面目錄
所以我猜測下面應該也能執行

```
   location ~* \.(js|jpg|png|css)$ {
        expires 30d;
    }
[Laravel nginx conf file](https://gist.github.com/ashleydw/afd389b1e763d3c1cf1f)
```

[vhosts - Serving html / css / js files with nginx - Stack Overflow](https://stackoverflow.com/questions/41330739/serving-html-css-js-files-with-nginx)
[nginx rewrite 指令 last break 区别-Tenderrain-51CTO 博客](https://blog.51cto.com/tenderrain/1949254)

## fastcgi

fastcgi_index 相對就是 nginx index 差不多東西

[nginx 配置里的 fastcgi_index 是用来干嘛？和 index 的区别是？ - 开源中国](https://www.oschina.net/question/92960_38798)

> 因此不再建议大家使用以下方式（搜了一下，网上大量的文章，并且 nginx.conf 的默认配置也是使用这种方式）：
> ~~fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; ~~
> ~~include fastcgi_params;~~
> 而推荐使用最新的方式：
> include fastcgi.conf;

fastcgi_split_path_info

> 舊版本的 nginx 使用如下方式配置
>
> ```
> location ~ .php($|/) {
>    set $script $uri;
>
>    set $path_info "";
>    if ($uri ~ "^(.+.php)(/.+)") {
>    >    set $script $1;
>    >    set $path_info $2;
>    }
>    fastcgi_pass 127.0.0.1:9000;
>    fastcgi_index index.php;
>    fastcgi_param SCRIPT_FILENAME $document_root$script;
>    fastcgi_param SCRIPT_NAME $script;
>    fastcgi_param PATH_INFO $path_info;
> }
> ```

> 新版本的 nginx 也可以使用 fastcgi_split_path_info 指令來設置 PATH_INFO，舊的方式不再推薦使用，在 location 段添加如下配置。
>
> ```
> location ~ ^.+.php {
> (...)
> fastcgi_split_path_info ^((?U).+.php)(/?.+)$;
>   fastcgi_param SCRIPT_FILENAME /path/to/php$fastcgi_script_name;
> fastcgi_param PATH_INFO $fastcgi_path_info;
>   fastcgi_param PATH_TRANSLATED $document_root\$fastcgi_path_info;
> (...)
> }
>
> ```

fastcgi_param 可設定環境參數
其他可參考[ngx_http_fastcgi_module 的那些事 - Web 开发手扎 - SegmentFault 思否](https://segmentfault.com/a/1190000002667095) {% asset_link phpfpm_1.png 備份圖 %}

這邊我還是搞不懂 phpfpm 跟 mod_php 到底有什麼差別
有 google 爬到一篇，秒懂

> FastCGI 是什麼?
> 接下來又提到那 FastCGI 是什麼呢? FastCGI 是用來提高 CGI 處理 process 性能用的
> 那 FastCGI 會怎麼實作呢?
> 當 php 啟動時、會去尋找 php.ini、進行環境的初始化，如果不使用 FastCGI 的情況下，每一個請求都會做這個動作，很明顯浪費系統資源，所以 FastCGI 會先啟動一個 master，解析配置文件用，接下來再啟動 worker，當請求過來時、master 會遞資訊給 worker，然後接下來等下一個請求，有這個機制就不用每一次重新跑一次初始化的動作了

[秒懂 PHP 的 FastCGI 跟 PHP-FPM 有什麼關係 - Astral web 歐斯瑞公司](https://www.astralweb.com.tw/what-is-differences-between-fastcgi-php-fpm/)

[[转] fastcgi.conf 与 fastcgi_params - - ITeye 博客](https://gxl-ct001.iteye.com/blog/2270522)

[\$\_SERVER['SCRIPT_FILENAME'] 与 **FILE** 区别 - 常斯特啰嗦司机 - 博客园](https://www.cnblogs.com/clphp/p/5106284.html)
[Nginx(PHP/fastcgi)的 PATH_INFO 配置 | 尘埃](http://wangying.sinaapp.com/archives/796)
[nginx 新手配置\$document_root - 海盗船的个人空间 - 开源中国](https://my.oschina.net/zhichi2011/blog/118409)
[nginx 使用 arg\_重写带参数 url 案例-明天过后-51CTO 博客](https://blog.51cto.com/leeyan/1865448)
