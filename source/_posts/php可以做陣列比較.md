---
title: php可以做陣列比較
date: 2018-06-24 00:46:56
tags: [php,array]
categories: PHP
---

今天無聊試著用array比較
發現竟然var_dump東西竟然是true

依我印象中Java、JavaScript都不能直接做array比較
但是php竟然可以....
太神奇了


<!--more-->

```php
$a = array(array('a','b'));
$b = array(array('a','b'));
var_dump($a == $b );
```

{% asset_img array_operators.png array_operators %}


array也可以用+法，但是+到對印的value還是以左邊為主(本身)
其實自己測試會比較清楚

>The + operator returns the right-hand array appended to the left-hand array; for keys that exist in both arrays, the elements from the left-hand array will be used, and the matching elements from the right-hand array will be ignored. 
```php

<?php
$a = array("a" => "apple", "b" => "banana");
$b = array("a" => "pear", "b" => "strawberry", "c" => "cherry");

$c = $a + $b; // Union of $a and $b
echo "Union of \$a and \$b: \n";
var_dump($c);

$c = $b + $a; // Union of $b and $a
echo "Union of \$b and \$a: \n";
var_dump($c);

$a += $b; // Union of $a += $b is $a and $b
echo "Union of \$a += \$b: \n";
var_dump($a);
?>

```
Union of $a and $b:
array(3) {
  ["a"]=>
  string(5) "apple"
  ["b"]=>
  string(6) "banana"
  ["c"]=>
  string(6) "cherry"
}
Union of $b and $a:
array(3) {
  ["a"]=>
  string(4) "pear"
  ["b"]=>
  string(10) "strawberry"
  ["c"]=>
  string(6) "cherry"
}
Union of $a += $b:
array(3) {
  'a' =>
  string(5) "apple"
  'b' =>
  string(6) "banana"
  'c' =>
  string(6) "cherry"
}

array比較 === 跟 ==差別
在於===會檢查key順序
```php
  $a = array (0 => "apple", 1 => "banana");
  $b = array (1 => "banana", 0 => "apple");

  var_dump($a === $b); // prints bool(false) as well

  $b = array ("0" => "apple", "1" => "banana");

  var_dump($a === $b); // prints bool(true)

  $b = array ("0" => "apple-1", "1" => "banana-1");

  var_dump($a === $b); // prints bool(false)
```


[PHP: Array Operators - Manual](http://www.php.net/manual/en/language.operators.array.php)
[PHP - Check if two arrays are equal - Stack Overflow](https://stackoverflow.com/questions/5678959/php-check-if-two-arrays-are-equal)
[PHP 三十天就上手-Day -17 Array Operators - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10053754)