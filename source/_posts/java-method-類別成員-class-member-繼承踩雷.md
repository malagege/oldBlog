---
title: java method 類別成員(class member) 繼承踩雷
date: 2018-08-14 22:36:52
tags: [java,php,JavaScript,class]
categories: 寫程式一些事
---


這篇先記錄最近改報表所踩到雷
一直以為我覺得class member也可以做java Overwrite
但overwrite好像是指method
在做繼承方發一想不到事情

<!--more-->


```java=
public class A{
    int usertype = 0;
    public void ok(){
        System.out.println(usertype);
    }
    public static void main(String args[]) {
        C c = new C();
        c.ok();
    }
}

class B extends A{
    // int usertype = 2;
    B(){
        usertype = 2;
    }
}

class C extends B{
    // int usertype = 3;
    C(){
        usertype = 3;
    }
}

```
結果印出應該是3

但是把usertype=2跟usertype=3前面注解拿掉
印出來的東西會是`0`
這個雷...


做後原本想做js做做看

JS class不能用class member...
發現typescript也不行
可能真的在class member轉function行不通

又好奇試試PHP
結果發現跟JAVA完全不一樣的結果


PHP

```php
<?php
class A{
    public $a = 1;
    public function ok(){
        echo $this->a;
    }
}

class B extends A{
    public $a =2;
}

class C extends B{
    public function __construct()
    {
        $a =3;
    }
}


$o = new C();
$o->ok();
//print 3
```
PHP class比較像JAVA，但結果不一樣...，我感到意外
不過...抽象類別  裡面方法不能用抽象function
詳細[抽象類別（Abstract Class） · PHP 學習筆記](https://kejyuntw.gitbooks.io/php-learning-notes/class/abstract-class.html)

python,golang我都不就不試了orz

[D2:［Java］類別的繼承（基本範例解說） - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10184771)
