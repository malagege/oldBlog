---
title: PHP的array_slice與array_splice差別
date: 2018-05-14 20:57:05
tags: [PHP,JavaScript,splice,slice,array]
categories: PHP
---

## 回傳值

`array_slice`回傳是提取出來陣列
`array_splice`回傳是提取出來陣列

看來return 出來東西都是一樣

## 帶進度array參數

* `array array_slice` ( array $array , int $offset [, int $length = NULL [, bool $preserve_keys = FALSE ]] )
* `array array_splice` ( array `&`$input , int $offset [, int $length = count($input) [, mixed $replacement = array() ]] )

會發現上面兩者回傳都一樣，但帶進去陣列參數執行結果不一樣

array_splice大致上跟JavaScript的splice一樣

## 結論

兩者最大不同是一個call by reference一個call by value
怎麼看呢?看文件上面有個`&`


PHP Object好像也是call by reference(address)

參考來源:
* [php - why array_slice() and array_splice() doing the same? - Stack Overflow](https://stackoverflow.com/questions/12932529/why-array-slice-and-array-splice-doing-the-same)
* [PHP: how to 'cut' my array? - Stack Overflow](https://stackoverflow.com/questions/3585966/php-how-to-cut-my-array/3585995#3585995)
* [PHP: Objects and references - Manual](http://php.net/manual/en/language.oop5.references.php)
* [10件在PHP7中不要做的事情 | 程序师](http://www.techug.com/post/10-things-not-to-do-in-php-7.html)
