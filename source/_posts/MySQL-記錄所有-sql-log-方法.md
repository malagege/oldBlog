---
title: MySQL 記錄所有 sql log 方法
date: 2019-07-11 21:15:11
tags: [php, mysql, log]
categories: SQL
---

最近聽說(同事)PHP 用 die 的話，可能不會關掉 PDO 連線
但聽說規聽說，Google 都沒有爬到，但我覺得這個我可以自己實驗

<!--more-->

## 開 MySQL LOG 方法

有看到`log=/tmp/mysql.log`加在`[mysqld]`就可以了
但我一直失敗!!
不知道是不是我電腦是 window 關係

最後找到這篇可以正常執行
[mysql 在 my.cnf 中配置日志文件路径出错报错:[ERROR] /usr/sbin/mysqld: ambiguous option '--log=/var/lib/mysql/mysql.log' (log-bin, log_slave_updates)](https://www.jonceryang.com/blog/36)

> 解决方法
> 不要用 log 配置项，改用 general_log，具体配置如下：
> general_log=ON
> general_log_file=/tmp/mysql.log

其實這個跟我這次實驗沒關係
之後想研究 Laravel 想看她的結果

## PDO 查看 prepare SQL log 長什麼樣子

原本就是想開 log 看有沒有執行
但發現 window 這段就會無效
有空我再找 linux 電腦測試
[php - PDO 操作，设置了 ATTR_EMULATE_PREPARES 属性为 false 后发现还是在本地模拟 prepare - SegmentFault 思否](https://segmentfault.com/q/1010000005714538)

## PHP die/exit 會做的事情

經過我測試，die 後會馬上關閉 pdo 連線
這是為什麼呢?我好奇去官網找一下答案

> Terminates execution of the script. Shutdown functions and object destructors will always be executed even if exit is called.
> exit is a language construct and it can be called without parentheses if no status is passed.

[PHP: exit - Manual](https://www.php.net/manual/en/function.exit.php)

好了，確定沒有這個問題，可以放心的使用這個東西
