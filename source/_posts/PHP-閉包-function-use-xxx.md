---
title: 'PHP 閉包 function() use (xxx){}'
date: 2018-08-12 15:05:14
tags: [php,閉包]
categories: PHP
---


一直都不是看很懂，PHP閉包語法要怎麼看
之前有學JavaScript閉包，但不是很明白PHP閉包跟它插在哪裡
不過最近有爬到這篇
<!--more-->

```php
//输出: This is a closure use string value lazy bind, msg is: Hello, everybody.<br />/n  
//换一种引用方式, 我们使用引用的方式来use  
//可以发现这次输出是闭包定义后的值...  
//这个其实不难理解, 我们以引用方式use, 那闭包use的是$msg这个变量的地址  
//当后面对$msg这个地址上的值进行了改变之后, 闭包内再输出这个地址的值时, 自然改变了.  
$msg = "Hello, everyone";  
$callback = function () use (&$msg) {  
    print "This is a closure use string value lazy bind, msg is: $msg. <br />/n";  
};  
$msg = "Hello, everybody";  
callback($callback);  
```

這個案例有點跟JavaScript閉包高達相似度
也能寫出跟JS閉包一樣的計時器
參考來源：
[php 5.3新增的闭包语法介绍function() use() {} - CSDN博客](https://blog.csdn.net/lgg201/article/details/6127564)
