---
title: '[PHP]basename採雷記'
date: 2018-06-24 00:28:10
tags: [basename,php,filepath]
categories: PHP
---

最近跟同事看smarty，覺得怪怪....
display(basename('xxxx.php',php));

後來參發現basename第二個參數不會帶.php
真的 點點點...

不熟這個function，一眼真的會猜錯...
這種小雷希望不要再採到


<!--more-->
```php
//顯示帶有文件擴展名的文件名
echo basename($path);

//顯示不帶有文件擴展名的文件名
echo basename($path,".php");
```