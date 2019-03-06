---
title: PHP intval跟(int)強制轉型差別
date: 2019-02-24 00:20:14
tags: [php]
categories: PHP
---

最近寫 PHP 使用 intval()做字串轉數值
之前有看到一篇文章，PHP 也有轉型`(int)'134'`
這兩個有沒有差別，當下我也不知道
只是`intval()`看起來很順眼
所以選擇這個方法
但我最近在 Google 搜尋
發現我錯了

<!--more-->

```
For years I've been using intval($var) for being sure something is an integer, and sometimes using (int)$var to cast it into an integer, which essentially does the same thing — learn what casting is.

I'm here to tell you that unless you require the second parameter of intval(), which changes the base, then you should be casting instead.

In the case of casting int, it's about 300% or 3 times as fast as using intval(), and if you use it a lot like I do, for example in setting the correct type/checking for SQL queries, then it's time to switch. I know I am.
```

[Casting int faster than intval in PHP – Tony Showoff](http://tonyshowoff.com/articles/casting-int-faster-than-intval-in-php/)
這篇文獻能看到 intval 是比較吃效能的

那我就想`intval()`有何作用?
[php - Is there any particular difference between intval and casting to int - `(int) X`? - Stack Overflow](https://stackoverflow.com/questions/1912599/is-there-any-particular-difference-between-intval-and-casting-to-int-int-x)
原來主要可以算 2 進位和 8 進位

現在仔細想想，JavaScript 也有一個`parseInt(xxx)`
這個第二個參數沒帶，第一個參數 0 開頭是`8位元算`
這個我曾經踩過，因為 uid 是 0 開頭，剛好要轉數字，跑出一個錯誤訊息，後來才知道轉成 8 位元
有聽到資深的前輩說後面第二個參數還是乖乖加個`10`會比較好

參考來源

- [php - Is there any particular difference between intval and casting to int - `(int) X`? - Stack Overflow](https://stackoverflow.com/questions/1912599/is-there-any-particular-difference-between-intval-and-casting-to-int-int-x)
- [Casting int faster than intval in PHP – Tony Showoff](http://tonyshowoff.com/articles/casting-int-faster-than-intval-in-php/)
- [Php string cast vs strval function which should I use? - Stack Overflow](https://stackoverflow.com/questions/7371991/php-string-cast-vs-strval-function-which-should-i-use)
- [php - Is there any particular difference between intval and casting to int - `(int) X`? - Stack Overflow](https://stackoverflow.com/questions/1912599/is-there-any-particular-difference-between-intval-and-casting-to-int-int-x)
