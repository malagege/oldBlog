---
title: JAVA BigDicmal 精度運算筆記 與  精度運算一些事
date: 2018-11-29 20:23:06
tags: [java,flat,bigdicmal]
categories: 程式心法
---

最近因為公司最近要改報表公式
因為對BigDicmal不是很熟
所以又特別翻了一下
這邊簡單說明一下，城市運算很大數字會失準
所以必須用別的方法算出正確數字

<!--more-->

```java
rs.getBigDecimal("num").multiply(new BigDecimal(Double.valueOf(1.245))).setScale(0, BigDecimal.ROUND_HALF_UP);
```
[瓦特希諾生活記錄: BigDecimal 四捨五入](http://fireball-catcher.blogspot.com/2012/09/bigdecimal.html)
[BigDecimal通过setScale设置小数位数发生ArithmeticException - 小木人印象](http://www.xwood.net/_site_domain_/_root/5870/5874/t_c257486.html)

setScale(0`, BigDecimal.ROUND_HALF_UP`);
後面`, BigDecimal.ROUND_HALF_UP`一定要帶入
否則當四捨五入會跳出Exception

>You have two BigDecimal numbers both of which require over a 1000 decimal places. Trying to set the scale to only have 113 decimal places means you will lose precision and therefore you need to round.

You can use the setScale methods that take a RoundingMode to prevent the exception but not the rounding.

[java - Rounding necessary with BigDecimal numbers - Stack Overflow](https://stackoverflow.com/questions/10734230/rounding-necessary-with-bigdecimal-numbers)




> System.out.println("12.5的四舍五入值：" + Math.round(12.5));
* [java提高篇(三)-----java的四舍五入 - chenssy - 博客园](https://www.cnblogs.com/chenssy/p/3366632.html)
* [使用BigDecimal进行精确运算 - chenssy - 博客园](https://www.cnblogs.com/chenssy/archive/2012/09/09/2677279.html)


* [Java Tutorial 第二堂（1）數值與字串型態 by caterpillar | CodeData](http://www.codedata.com.tw/java/java-tutorial-the-2nd-class-1-numeric-types-and-string/)
* [Java BigDecimal詳解，提供了豐富的四捨五入規則 - 掃文資訊](https://hk.saowen.com/a/b9a749edc4f667a5fe6260bb56bd5822807cb65d2312b5153f8c9a3171e5cbb1)
* [BigDecimal通过setScale设置小数位数发生ArithmeticException - 小木人印象](http://www.xwood.net/_site_domain_/_root/5870/5874/t_c257486.html)
* [BigDecimal.setScale 处理java小数用法 - 天涯海角的博客 - CSDN博客](https://blog.csdn.net/qq_36497605/article/details/70597318)
* [BigDecimal.setScale()方法 - lilie008的专栏 - CSDN博客](https://blog.csdn.net/lilie008/article/details/38318485)


### PHP   也有浮點數算術問題


```PHP浮點型在進行+-*%/存在不準確的問題
例如：
1.
$a = 0.1;
$b = 0.7;
var_dump(($a + $b) == 0.8);
打印出來的值為 boolean false
這是為啥?PHP手冊對於浮點數有以下警告信息:
Warning
浮點數精度
顯然簡單的十進制分數如同 0.1 或 0.7 不能在不丟失一點點精度的情況下轉換為內部二進制的格式。這就會造成混亂的結果：例如，floor((0.1+0.7)*10) 通常會返回 7 而不是預期中的 8，因為該結果內部的表示其實是類似 7.9999999999…。
這和一個事實有關，那就是不可能精確的用有限位數表達某些十進制分數。例如，十進制的 1/3 變成了 0.3333333. . .。
所以永遠不要相信浮點數結果精確到了最後一位，也永遠不要比較兩個浮點數是否相等。如果確實需要更高的精度，應該使用任意精度數學函數或者 gmp 函數
```
[黃聰：php精度計算問題 - 掃文資訊](https://hk.saowen.com/a/1a3866ccb1c370b2fbcba7841ef434c1b80246a5666867cb549023af83b2088e)
```php
echo .1 + .2;    //0.3
var_dump(.1 + .2); //float(0.30000000000000004441)

//  PHP echo converts 0.30000000000000004441 to a string and shortens it to “0.3”. To achieve the desired floating point result, adjust the precision ini setting: ini_set(“precision”, 17).
```
[0.30000000000000004.com/](https://0.30000000000000004.com/)

```
These functions are only available if PHP was configured with --enable-bcmath . 

bcmath ist not installed automatically with php 7 on linux (ubuntu server)
You have to use: sudo apt-get install php-bcmath
```
[PHP: Installation - Manual](http://php.net/manual/en/bc.installation.php)

## 倍精轉數字小筆記

### PHP倍精變成數字(字串)
```php
number_format(1.2378147769392E+14,0,'','')
```
[Convert exponential to a whole number in PHP - Stack Overflow](https://stackoverflow.com/questions/4964059/convert-exponential-to-a-whole-number-in-php/4964120#4964120)

### PHP 數字字串(with comma)轉乘數字
```php
$number = '1,000';
$number = (int) filter_var($number, FILTER_SANITIZE_NUMBER_INT);
var_dump($number);
```
Or
```php
$number = '1,000.5669';
$number = (float) str_replace(',', '', $number);
var_dump($number);
```
[getting int value from comma separated number php - Stack Overflow](https://stackoverflow.com/questions/25966365/getting-int-value-from-comma-separated-number-php)

### MySQL 倍精變成數字(字串)
```sql
select REPLACE(FORMAT(100000000000000000000000000000000000000, 0), ',', '')
```

MySQL 轉型型態方法
[[資料庫] 如何轉換 Mysql varchar type 到 int type | 小惡魔 - 電腦技術 - 工作筆記 - AppleBOY](https://blog.wu-boy.com/2010/12/%E8%B3%87%E6%96%99%E5%BA%AB-%E5%A6%82%E4%BD%95%E8%BD%89%E6%8F%9B-mysql-varchar-type-%E5%88%B0-int-type/)



[使用浮點數最最基本的觀念 / 一般討論區 / 程式設計俱樂部](http://www.programmer-club.com.tw/ShowSameTitleN/general/6768.html)
[為什麼在項目中要儘量避免使用浮點數，不使用浮點數，那該如何計算浮點數？ - 知乎](https://www.zhihu.com/question/65960911)
[CODE-分贓程式的寫法-黑暗執行緒](https://blog.darkthread.net/blog/about-money-distribution/)
什麼時候要用浮點數、什麼時候要用倍精?
我目前來看在算錢，尤其算拆分比(有用到乘除)算到有小數點會影響到
盡可能用bigdicmal這種東西
