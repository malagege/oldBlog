---
title: PHP 做浮點數需要注意的事情
date: 2019-08-04 14:03:09
tags: [php]
categories: PHP
---

最近公司做報表程式，看到裡面程式會 0.3254\*1000 先做運算
然後運算完後會`除1000`
原本猜想是不是有浮點數問題
但我印象 0.1+0.2=0.3，這部分 PHP 好像沒有什麼問題
[0.30000000000000004.com/](https://0.30000000000000004.com/)
但我今天看到這篇有提到[php 踩過的那些坑（1）浮點數計算 - 每日頭條](https://kknews.cc/zh-tw/finance/y86vrrj.html)

<!--more-->

> 二、防坑攻略：
> 1、通過乘 100 的方式轉化為整數加減，然後在除以 100 轉化回來……
> 2、使用 number_format 轉化成字符串，然後在使用（float）強轉回來……
> 3、php 提供了高精度計算的函數庫，實際上就是為了解決這個浮點數計算問題而生的。
> 原文網址：https://kknews.cc/finance/y86vrrj.html

使用 bcsub 方式可以解決問題，但記得後面要加入要算出來的浮點數

```php
$a = 2586;
$b = 2585.98;
var_dump($a-$b);
```

有找到這個文章原始來源
這幾篇，也不錯

- [php 踩过的那些坑(1) isset()详解 - 壁虎漫步. - 博客园](https://www.cnblogs.com/phpfensi/p/8143355.html) {% asset_link web1.png 備份圖 %}
- [php 踩过的那些坑(2) strpos 引发的血案 - 壁虎漫步. - 博客园](https://www.cnblogs.com/phpfensi/p/8143358.html) {% asset_link web2.png 備份圖 %}
- [php 踩过的那些坑(3) 数据类型转换 - 壁虎漫步. - 博客园](https://www.cnblogs.com/phpfensi/p/8143360.html) {% asset_link web3.png 備份圖 %}
- [php 踩过的那些坑(4) false,NULL,0,''详解 - 壁虎漫步. - 博客园](https://www.cnblogs.com/phpfensi/p/8143363.html) {% asset_link web4.png 備份圖 %}
- [php 踩过的那些坑（5）浮点数计算 - 壁虎漫步. - 博客园](https://www.cnblogs.com/phpfensi/p/8143367.html) {% asset_link web5.png 備份圖 %}

更多作者寫的內容

- [PHP - 随笔分类 - 壁虎漫步. - 博客园](https://www.cnblogs.com/phpfensi/category/594117.html)
