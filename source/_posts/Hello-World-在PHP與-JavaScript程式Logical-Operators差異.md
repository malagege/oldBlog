---
title: '''Hello World'' || '''' 在PHP與 JavaScript程式Logical Operators差異'
date: 2018-08-18 11:08:47
tags: [javascript,PHP,Logical operators]
categories: PHP
---
PHP和JavaScript同樣兩個都是軟型態
大同小異，但做這個Logical Operators結果不一樣

最近不小心在PHP寫出一個bug
被同事抓到了
同一個雷不要在踩
<!--more-->

## PHP
PHP一定回傳boolean
```php
$a = 0 || 'avacado';
print "A: $a\n";
```
>will print:
A: 1

{% asset_img php_lo.png "PHP Logical Operators" %}
[PHP: Logical Operators - Manual](http://php.net/manual/en/language.operators.logical.php#77411)


## JavaScript
`'ex1'||'ex2'`true傳回'ex1' false 傳噡'ex2'

不過剛看了下面MDN寫的內容
{% asset_img js_lo.png "JavaScript Logical Operators" %}
[Logical Operators - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Description)

還有`'ex1'&& 'ex2'`
true傳回ex2
false傳回ex1

大部份比較常在JS看到 `||`，但是`&&`真的比較少看到
