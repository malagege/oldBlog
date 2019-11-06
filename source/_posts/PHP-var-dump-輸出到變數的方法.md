---
title: PHP var_dump 輸出到變數的方法
date: 2019-10-30 20:34:50
tags: [php,var_dump]
categories: PHP
---

實務上 log 不是只會記在頁面上
也會在 console 裡面

<!--more-->

```php
<?php
ob_start();
var_dump($party);
$result = ob_get_clean();
Log::write('result => '. $result , "xxxxxxx" );
```