---
title: PHP CI 框架不會記錄 ERROR 到 nginx log裡解決方案
date: 2019-06-03 22:47:56
tags: [php, nginx]
categories: PHP
---

[PHP-FPM 打開 error log 方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/05/22/PHP-FPM-%E6%89%93%E9%96%8B-error-log-%E6%96%B9%E6%B3%95/)
之前紀錄 PHP-FPM ERROR 錯誤方法
但今天發現公司的 CI 還是沒有紀錄 LOG
追查了一下原因，原來 CI 有做 `set_error_handler` ...
這邊詳細記錄這個雷

<!--more-->

事情是這樣的
n 個月前，我們公司用了 CI 做開發
由於第一次把 CI 放在 docker container 下
結果查看 log 的時候竟然沒有紀錄 log ...
當初以為是 docker 或 php-fpm 設定的問題
或者 display_error 或 error_reporting 問題

但今天發現是 CI 問題!!!!

## 發現事情不單純

CI 故意寫錯 log，但是 nginx docker 上面竟然就是沒有 log
但發現直連 `test.php`(非用 CI 框架)正常
這時候我就測試在 `index.php` 幾行都會記入到 nginx log
但發現放在 `index.php` 最下面錯誤不會出來

```php
<?php
...
require_once BASEPATH.'core/CodeIgniter.php';
```

這時候我覺得 Codeigniter 有鬼

有猜到是 Codeigniter.php 裡面的這三行搞鬼
注解掉就可以用了

```php
<?php
	set_error_handler('_error_handler');
	set_exception_handler('_exception_handler');
    register_shutdown_function('_shutdown_handler');
```

有找一下，set_error_handler

> It is important to remember that the standard PHP error handler is completely bypassed for the error types specified by error_types unless the callback function returns FALSE. error_reporting() settings will have no effect and your error handler will be called regardless - however you are still able to read the current value of error_reporting and act appropriately. Of particular note is that this value will be 0 if the statement that caused the error was prepended by the @ error-control operator.
> [PHP: set_error_handler - Manual](https://www.php.net/manual/en/function.set-error-handler.php)
> 簡單說就是錯誤，會執行 set_error_handler
> 但是預設錯誤的 function 不會執行
> 除非裡面 return false(目前還沒測差過)

不過 CI 沒有記錄 php stderr ，所以導致 CI 沒有紀錄 (PS: 這個是我的猜測)
[简述 PHP 的标准输入与输出 | Laravel China 社区](https://learnku.com/articles/5765/brief-introduction-of-standard-input-and-output-of-php)
目前猜測可能寫 stderr 會出現到 log 上面

**2019-06-04 小記**
今天公司小測
使用`file_put_contents('php://stderr','this is a log');`
會被 php-fpm 記錄起來，但是 nginx 好像不會收到
目前猜測可能不是從 php-fpm 寫進去...
所以才不會記錄(?)
但已經能解決自行寫入 log 問題
正常接到 php-fpm 的 stderr (nginx docker)

```
  thrown in /usr/local/nginx/html/index.php on line 364" while reading response header from upstream, client: 10.56.158.42, server: localhost, request: "GET / HTTP/1.1", upstream: "fastcgi://192.168.112.8:9000", host: "10.56.159.10",
10.56.158.42 - - [03/Jun/2019:09:42:46 +0000] "GET / HTTP/1.1" 200 238 "-" "PostmanRuntime/7.13.0",
2019/06/03 09:43:36 [error] 6#6: *46 FastCGI sent in stderr: "PHP message: PHP Fatal error:  Uncaught Error: Call to undefined function dffffffffffffff() in /usr/local/nginx/html/index.php:364,
```

error_log('this is a log .',0);
也能做到一樣的事情
但一樣 nginx 那一邊不會出現 log ，只會在 nginx
不知道為什麼...
[PHP: error_log - Manual](https://www.php.net/manual/en/function.error-log.php)
3 選項可以寫路徑，但感覺會沒有權限寫的感覺
有空試試

CI 有做 set_error_handler
http code 500 錯誤
所以可能開啟`display_error,1`時候
http code 碼會是 200

> When display_errors is on it will return 200 always.
> When display_errors is off it will return 500.
> [Apache/PHP returns HTTP Status Code 200 on error pages - Stack Overflow](https://stackoverflow.com/questions/28972933/apache-php-returns-http-status-code-200-on-error-pages)

就先研究到這邊
[redirect php-fpm output to nginx's error log](https://gist.github.com/joerx/87c6755023d2817154b41264311fcfe6)

---

最後在[CI 框架不写 Nginx 错误日志的原因分析及解决办法 - zhang197093 的博客 - CSDN 博客](https://blog.csdn.net/zhang197093/article/details/75215812)
看到真的有人遇到一樣的問題

這個太雷了 冏!!!

其他參考

[PHP: 预定义常量 - Manual](https://www.php.net/manual/zh/errorfunc.constants.php)
[PHP: restore_error_handler - Manual](https://www.php.net/manual/zh/function.restore-error-handler.php)
[CI 框架不写 Nginx 错误日志的原因分析及解决办法 - zhang197093 的博客 - CSDN 博客](https://blog.csdn.net/zhang197093/article/details/75215812)
