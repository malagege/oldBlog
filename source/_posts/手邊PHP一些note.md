---
title: 手邊PHP一些note...
date: 2018-11-15 23:40:03
tags:
categories:
---

最近發現PHP function宣告寫在下面
竟然上面可以呼叫...
我以為是JavaScript的hoisting一樣的東西？
剛好看的[我知道你懂 hoisting，可是你了解到多深？ | TechBridge 技術共筆部落格](https://blog.techbridge.cc/2018/11/10/javascript-hoisting/?fbclid=IwAR0DxYCVyKD-y1WkcFn3IumG9IUlGd6GyASAeMhvRGe13C4qK1ps_5e6-pw)
只有略懂略懂...
但為什麼可以呼叫呢？
目前還沒找到原因
所以先整理出來

<!--more-->

在這邊我先說我的猜測**不一定是正確原因**
原本想說PHP的function name會不會跟`常數`是用同一個地方
結果是了一下，發現常數竟然有分大小寫，但感覺不會跟變數衝到....
好神奇，可能JS寫太久，覺得怪怪的 哈哈

* [PHP - calling a function before it is declared - Stack Overflow](https://stackoverflow.com/questions/27211326/php-calling-a-function-before-it-is-declared)

> Functions need not be defined before they are referenced, except when a function is conditionally defined as shown in the two examples below.

這邊就說明了function放後面，可以呼叫。還可以透過`Conditional functions`這東西，這也滿奇怪的
* [PHP: 用户自定义函数 - Manual](http://php.net/manual/zh/functions.user-defined.php)

中間有想到是不是`常數`跟function一樣可以放後面
但**答案是不行的**



* [PHP: 超全局变量 - Manual](http://php.net/manual/zh/language.variables.superglobals.php)

* [彻底搞懂PHP的变量作用域和全局变量 - 简书](https://www.jianshu.com/p/09217582cc08)
```php
function test() { 
    global $a; 
    unset($a); 
} 
$a = 1; 
test(); 
echo $a; 
```
结果

    明明是unset了呀，为什么还会打印出来呢？
    众所周知，我们的function里面的永远是个私有变量，unset的确是起作用了，它unset了一个global 的值呀，而global在函数产生一个指向函数外部变量的别名变量，而不是真正的函数外部变量；$GLOBALS[]确确实实调用是外部的变量，函数内外会始终保持一致！

作者：michaelgbw
链接：https://www.jianshu.com/p/09217582cc08
來源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。

老實說...，我還是沒有搞清楚
但這樣看下去，可能還有static要看

* [PHP: Userland Naming Guide - Manual](http://php.net/manual/en/userlandnaming.php)
* [PHP 5.3/6 新增功能 - Namespace - 石頭閒語](http://rocksaying.tw/archives/10814499.html)

* [PHP 必須要知道的小細節 · PHP7 開發之道](https://imyoungyang.gitbooks.io/php7-study-group-notes/content/Chapter2/php-something-should-know.html)

> define() vs const 常數定義
> 最主要的原因是底層的不同
    define() 是 run-time constants 而 const 是 compile-time constants.
    define() 是 global scope 或者在某個 namespace，所以不能用來定義 class-scope 的常數。
    define()可以用在 if() 但 const不能用在 if()

看到這邊還真的不知道這兩個不太一樣orz

* [PHP严重致命错误处理:php Fatal error: Cannot redeclare clas - 小星星程序员的个人空间 - 开源中国](https://my.oschina.net/u/1766067/blog/299888)

function,class重複宣告會有問題

* [PHP中的提升(Hoisting) | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/573568/)

這篇有簡易測試Hoisting，不過我認為跟js的hoisting不一樣
還有測試code
```php
$poor_function = function(){
};
var_dump(function_exists('poor_function'));
```
這樣是沒有意義的
可以參考下面裡面
* [Use Variable as Function Name in PHP - Stack Overflow](https://stackoverflow.com/questions/8466473/use-variable-as-function-name-in-php)
不過我有測試`var_dump($poor_function)`顯示是一個閉包


* [PHP中定义常量的区别，define() vs. const - 简书](https://www.jianshu.com/p/a38b81433183)
這邊有紀錄很多define()跟const差異


其他要紀錄的
* [PHP 底层的运行机制与原理 - 文章 - 伯乐在线](http://blog.jobbole.com/94475/)
* [CGI、FastCGI和PHP-FPM关系图解 - 歪麦博客](https://www.awaimai.com/371.html?fbclid=IwAR1dC7s3yKPjJM-PdYTQoAudEkRpG17LgOXc-h1kM_I0K4ZzxHLGq9H2ML8)
* [PHP function to generate v4 UUID - Stack Overflow](https://stackoverflow.com/questions/2040240/php-function-to-generate-v4-uuid)

結論，我還是不懂PHP  QQ