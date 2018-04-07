---
layout: post
title: '在Window用XAMPP架CodeIgniter3'
date: 2017-06-18 06:12
comments: true
categories: CodeIgniter
tags: [CodeIgniter, PHP, XAMPP]
---
最近有在學CodeIgniter3，想說再不敢快作筆記
會忘了特別快，雖然採用快速架站的XAMPP來架
但也有遇到一點點問題
在此做一下筆記

<!--more-->

下載XAMPP解壓縮到你要裝的地方
再來`setup_xampp.bat`他會對XAMPP做設定
然後就可以啟用`xampp-control.exe`

再這之前
在把CodeIgniter3丟到htdocs目錄下
打開瀏覽器`http://localhost/ci3`(PS:我資料夾改成ci3)
```
Welcome to CodeIgniter!

The page you are looking at is being generated dynamically by CodeIgniter.

If you would like to edit this page you'll find it located at:
application/views/welcome_message.php

The corresponding controller for this page is found at:
application/controllers/Welcome.php

If you are exploring CodeIgniter for the very first time, you should start by reading the User Guide.
```
看到這個畫面就成功了

接下來我們要去設定DB設定

首先去config.php把database啟用
`$autoload['libraries'] = array('database');`
通常session也滿常用的，通常我也會順便啟用session
`$autoload['libraries'] = array('database','session');`

這個也滿常用的
`$autoload['helper'] = array('url');`

接下來就是設定database.php
預設XAMPP沒有設定root帳號、密碼
但我這邊就不設定，正式還境通常要設定喔

```php
$db['default'] = array(
	'dsn'	=> '',
	'hostname' => 'localhost',
	'username' => '',
	'password' => '',
	'database' => 'test',
	'dbdriver' => 'mysqli',
	'dbprefix' => '',
	'pconnect' => FALSE,
	'db_debug' => (ENVIRONMENT !== 'production'),
	'cache_on' => FALSE,
	'cachedir' => '',
	'char_set' => 'utf8',
	'dbcollat' => 'utf8_general_ci',
	'swap_pre' => '',
	'encrypt' => FALSE,
	'compress' => FALSE,
	'stricton' => FALSE,
	'failover' => array(),
	'save_queries' => TRUE
);
```
再開瀏覽器`http://locahost/ci3`，顯示正常
這樣架設就算完成

有時你ci3不是載在第一層目錄
請記得照下面設定
```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /(目錄path)/index.php/$1 [L]
```