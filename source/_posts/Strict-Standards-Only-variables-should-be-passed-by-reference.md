---
title: "Strict Standards: Only variables should be passed by reference"
date: 2019-05-09 21:34:52
tags: [php]
categories: PHP
---

最近 php 預設設個錯誤
但我程式沒有寫錯...
後來找了一下，參照不能代入 function 裡面
如這個`$price = array_shift(get_product_price($product_key));`

因為忘了第二次，所以獨立寫一篇

```php
$product_price = get_product_price($product_key);
$price = array_shift($product_price);
```

[臨時筆記: ［PHP］Error: Only variables should be passed by reference](http://temporarynotesssss.blogspot.com/2015/03/phperror-only-variables-should-be.html)
[PHP 有關參照(Reference)的那些事 | 程式狂想筆記](https://malagege.github.io/blog/2019/01/28/PHP%E6%9C%89%E9%97%9C%E5%8F%83%E7%85%A7-Reference-%E7%9A%84%E9%82%A3%E4%BA%9B%E4%BA%8B/)其實我以前就有提到 orz
