---
title: PHP 陣列 array_unique 踩雷小記
date: 2019-04-21 16:13:07
tags: php
categories: PHP
---

最近用 array_unique
[PHP: array_unique - Manual](https://www.php.net/manual/en/function.array-unique.php)
他可以過濾重複的內容
但不小心用出一個 bug

例如有一個`$array = [0,1,2,2,3,4];`
發現`array_unique(array);`
var_dump 一看，key 發現有跳號

<!--more-->

```php
<?php
$array = [0,1,2,2,3,4];

var_dump($array);
echo PHP_EOL;
var_dump(array_unique($array));
```

```
array(6) {
  [0]=>
  int(0)
  [1]=>
  int(1)
  [2]=>
  int(2)
  [3]=>
  int(2)
  [4]=>
  int(3)
  [5]=>
  int(4)
}

array(5) {
  [0]=>
  int(0)
  [1]=>
  int(1)
  [2]=>
  int(2)
  [4]=>
  int(3)
  [5]=>
  int(4)
}
```

所以這時候做`for($a = 0; $a < count($a); $a++)`會有問題

這邊有想到兩個做法

1. 改 foreach
2. array_values(\$array)

可以解決
