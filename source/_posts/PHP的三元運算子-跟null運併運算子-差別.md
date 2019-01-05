---
title: "PHP的三元運算子(?:)跟null運併運算子(??)差別"
date: 2019-01-05 16:59:56
tags: [php]
categories: PHP
---

最近發現 PHP 有兩個運算子`?:`和`??`
這兩個看起來用途很像
但我覺得一定有什麼不一樣的地方
這邊就簡單記錄他們的差別

<!--more-->

## ?:

首先，先介紹這個符號，有些別的程式語言也可以用這個
他有專門中文名稱叫「三元運算子」
我還有一篇看他他叫貓王運算子?!!!
原來是`?:`反過來看很像貓王 XDD
詳細：[貓王運算子 - Elvis Operator - Qiita](https://qiita.com/vc7/items/5519b3721377362d7c1d)

官方文件敘述[PHP: 比较运算符 - Manual - 三元運算符 ](http://php.net/manual/zh/language.operators.comparison.php#language.operators.comparison.ternary)

> 表達式 (expr1) ? (expr2) : (expr3) 在 expr1 求值為 TRUE 時的值為 expr2，在 expr1 求值為 FALSE 時的值為 expr3。
> 自 PHP 5.3 起，可以省略三元運算符中間那部分。表達式 expr1 ?: expr3 在 expr1 求值為 TRUE 時返回 expr1，否則返回 expr3。

## ??

這個是我同事跟我說 PHP7.0 才可以支援使用
簡單看了一下介紹，感覺跟`?:`很像
但是不知道差別在哪
其實官方文件伈得很清楚[PHP: 新特性 - Manual - null 合併運算符](http://php.net/manual/zh/migration70.new-features.php#migration70.new-features.null-coalesce-op)

> 由於日常使用中存在大量同時使用三元表達式和 isset()的情況， 我們添加了 null 合併運算符 (??) 這個語法糖。如果變量存在且值不為 NULL， 它就會返回自身的值，否則返回它的第二個操作

```php
<?php
// Fetches the value of $_GET['user'] and returns 'nobody'
// if it does not exist.
$username = $_GET['user'] ?? 'nobody';
// This is equivalent to:
$username = isset($_GET['user']) ? $_GET['user'] : 'nobody';

// Coalesces can be chained: this will return the first
// defined value out of $_GET['user'], $_POST['user'], and
// 'nobody'.
$username = $_GET['user'] ?? $_POST['user'] ?? 'nobody';
?>
```

這樣來看，在`??`使用上會比`?:`來的方便，但缺點是只有在 PHP7.0 專案才能使用

## 簡單總結

其實，這兩個都非常接近，`??`在沒有宣告的時候不會發生錯誤，`?:`會發生錯誤。
但這兩個就算了解，不常使用下也很容易忘記跟部習慣...
我後來發現這種跟[mysql COALESCE 取代 null 值好方法 | 程式狂想筆記](https://malagege.github.io/blog/2018/07/19/mysql-COALESCE-%E5%8F%96%E4%BB%A3null%E5%80%BC%E5%A5%BD%E6%96%B9%E6%B3%95/)有點像
當`??`為 null 時候就用後面的變數`??`，這個跟`COALESCE`有異曲同工之妙
所以用 SQL 這個觀念來記真的比較輕鬆

## 簡單實測紀錄

{% asset_img 1.jpg  %}
{% asset_img 2.jpg  %}
