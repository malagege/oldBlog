---
title: php pcntl_fork 多程序小記
date: 2019-11-21 20:43:51
tags: [php]
categories: PHP
---

之前在 swoole 有簡單紀錄 pcntl_fork
我在寫那篇之前，以為 pcntl_fork 執行子程序大概不會收到執行結果
不過今天仔細看，可以接收到程式執行結果
但還是不能傳接收參數值

<!--more-->

## 簡單範例

[多采多姿的程式筆記: 利用PHP寫Multi Process程式 1 - 產生子程序](http://pkwbim-programming-note.blogspot.com/2008/01/phpmulti-process-1.html)
基本上裡面範例可以直接拿來改
建議先了解 fork 子程序概念會是最好

[多采多姿的程式筆記: 利用PHP用寫Multi Process程式 - 2 使用信號機](http://pkwbim-programming-note.blogspot.com/2008/01/phpmulti-process-2.html)
ftok  swoole 裡面有 Message Quene 有提到

## pcntl_wait、pcntl_waitpid 

[php多进程总结 - leezhxing - 博客园](https://www.cnblogs.com/leezhxing/p/5223289.html){% asset_link web2.png 備份圖 %}

裡面也有提 殭屍程序

[八克里: PHP Multi Process 程式以 PING 多主機應用為範例](http://blog.jangmt.com/2015/02/php-multi-process-ping.html)
備份[mylinux_document/ping.php at master · malagege/mylinux_document](https://github.com/malagege/mylinux_document/blob/master/ping.php)

## DB 連線問題
目前有簡單實驗，父子 DB 連線是可以共用的
但不建議這樣做，同時在跑的程式結果有做 交易(translation) 就很麻煩
所以還是分開來會比較好
[pcntl_fork 導致 MySQL server has gone away 解決方案 - AllenChou - 博客園](https://www.cnblogs.com/AllenChou/p/6607182.html) {% asset_link web3.png 備份圖 %}

**2019-12-04**
建議做 fork 時候最好先把連線關掉
完成重新做連線會比較好
否則會預到很多問題

## Daemon

[PHP实现daemon | Nginlion](https://anyof.me/articles/489) {% asset_link web4.png 備份圖 %}
[如何优雅地运行一个 Daemon](http://wsfdl.com/linux/2015/10/18/%E5%A6%82%E4%BD%95%E4%BC%98%E9%9B%85%E7%9A%84%E8%BF%90%E8%A1%8C%E4%B8%80%E4%B8%AAdaemon.html)
[PHP多进程编程 | 第二世](https://sunzy.me/2017/09/php-multi-process/) {% asset_link web5.png 備份圖 %}

### 彩蛋
[shaneharter/PHP-Daemon: Build production-ready Daemons using PHP 5.3+. Build fault-tolerant applications without the boilerplate.](https://github.com/shaneharter/PHP-Daemon)
[深入理解Linux修改hostname - 潇湘隐者 - 博客园](https://www.cnblogs.com/kerrycode/p/3595724.html)
[Bash 程式設計教學：平行執行背景子行程，用 wait 等待工作結束 - G. T. Wang](https://blog.gtwang.org/programming/bash-tutorial-parallel-subprocesses-and-wait/)