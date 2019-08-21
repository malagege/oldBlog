---
title: "PHP -q 是什麼指令"
date: 2019-08-14 22:13:53
tags: [php]
categories: PHP
---

最近看到公司舊的排程有這個指令
`php -h` 沒有看到
後來官網看到被廢棄掉

<!--more-->

稍微研究了一下
好像跟以前執行程式有關係

```
 $ php-cgi p.php

    X-Powered-By: PHP/5.5.18
    Content-type: text/html
    hello world

You can suppress these headers:
$ php-cgi -q p.php
    hello world
The php executable in recent versions of PHP doesn't need this. Using -q or not using -q gives the same result:
    $ php -q p.php
hello world
$ php p.php
hello world
```

參考來源:
[What does 'PHP -q' do as opposed to 'PHP' when executing a PHP script from command line? - Quora](https://www.quora.com/What-does-PHP-q-do-as-opposed-to-PHP-when-executing-a-PHP-script-from-command-line)

排程
[用于监控 cron 作业和自动化任务的系统？ - VoidCC](http://cn.voidcc.com/question/p-zeaiytux-bbx.html)
