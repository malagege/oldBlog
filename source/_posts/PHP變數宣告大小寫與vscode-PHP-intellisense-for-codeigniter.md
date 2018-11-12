---
title: PHP變數宣告大小寫與vscode PHP intellisense for codeigniter
date: 2018-10-23 22:31:47
tags: [vscode,php]
categories: PHP
---

今天發現PHP變數大小事情
還是小記，怕以後忘記
<!--more-->

 class, function 命名呼叫可以大小寫沒有分
 變數,和class屬性命名大小寫有分

 後來簡單測試一下，感覺PHP `變數`跟`函式`可以撞名
 `變數`分大小寫
 `函式`不分大小寫
這樣就通了

還簡單做了小實驗
```php
<?php
class a{
    public $b = 'test';
    function b(){
        echo 'test2';
    }
}

$a = new A;
$a->B();
```


```php
<?php
class a{
    public $b = 'test';
    function a(){
        echo 'test2';
    }
}

$a = new A;
$a->A();
```
跑出兩個`test2test2`
~~不小心測出意料結果，別緊張~~
這是因為`建構式`

PHP7.0開始會有提示
```
<br />
<b>Deprecated</b>:  Methods with the same name as their class will not be constructors in a future version of PHP; a has a deprecated constructor in <b>[...][...]</b> on line <b>4</b><br />
```


[PHP的細節觀念與撰寫習慣 – Bryce'S Note](http://blog.twbryce.com/php-writing-habit-detailed-concepts/)


## vscode PHP intellisense for codeigniter

[PHP intellisense for codeigniter - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=small.php-ci)

安裝這個可以方便在vscode寫CI
不過...由於我公司開發規範第一個檔名都要大寫...
所以呼叫mod那邊好像怪怪的
但方便PHP intellisense都是好套件XD

但注意需要放置`system`
由於我們公司專案沒有在底層放system
所以需要自己copy一份`system`到目錄
或去vscode設定那邊調整位置

