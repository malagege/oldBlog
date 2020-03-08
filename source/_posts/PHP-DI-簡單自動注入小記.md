---
title: PHP-DI 簡單自動注入小記
date: 2020-02-10 21:10:54
tags: [php,di]
categories: PHP
---

最近跑去看 PHP-DI 
簡單實作小記

<!--more-->

## 簡單講解

在使用 Spring boot 和 Laravel 上，常常看到自動注入的範例
很多文章沒有深入講解為什麼可以做自動注入

這段時間我使用到 Spring boot 的 `@autowired` 做自動注入
明白在框架中有有一個機制在控制你裡面放的需要 `new` 出來的物件
在使用上可以說
最近再回頭看 php-di 就突然了解了

更多講解可以看[Laravel 實作小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/07/16/Laravel-%E5%AF%A6%E4%BD%9C%E5%B0%8F%E8%A8%98/#%E6%9C%8D%E5%8B%99%E5%AE%B9%E5%99%A8)
之前有記錄幾個網站有詳細談到


## 自動注入

```php
<?php
include './vendor/autoload.php';

# 第 1 步: 建立 DI container
$builder = new \DI\ContainerBuilder();
$container = $builder->build();

# 第 2 步: 建立 class 
$a = $container->get('A');

var_dump($a);

# 第 3 步: 建立 DI 實作 class，B class 不需要做 new 動作
class A{
    private $_b;
    function __construct(B $b)
    {
        $this->_b = $b;
        // $b->hello();
    }
}

class B{
    function __construct()
    {
        echo "Hello World! B";
    }
    function hello()
    {
        echo "B:say hello";
    }
}


```

## autowiring 出來都是單例模式

```php
<?php
include './vendor/autoload.php';


$builder = new \DI\ContainerBuilder();
$container = $builder->build();


$a = $container->get('A');
$a_b = $a->get_B();
$b = $container->get('B');
var_dump($a_b === $b); # true

class A{
    private $_b;
    function __construct(B $b)
    {
        $this->_b = $b;
        // $b->hello();
    }

    function get_B()
    {
        return $this->_b;
    }
}

class B{
    function __construct()
    {
        echo "Hello World! B";
    }
    function hello()
    {
        echo "B:say hello";
    }
}


```

BTW: [Spring IOC 单例和多例 - 简书](https://www.jianshu.com/p/6f0b148d698b)
[hailindai/SSMSample](https://github.com/hailindai/SSMSample)

