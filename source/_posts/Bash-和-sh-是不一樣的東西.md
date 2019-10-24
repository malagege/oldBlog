---
title: Bash 和 sh 是不一樣的東西
date: 2019-10-18 20:01:38
tags: [bash,sh]
categories: Linux
---

最近使用 sh xxx.sh 出現這個錯誤`[[: not found`
奇怪，我看語法正確怎麼會有問題
原來發現 sh 跟 bash 是不一樣

改用 bash xxx.sh 就能正常了

<!--more-->

> sh只是一個符號鏈接，最終指向是一個叫做dash的程序，自Ubuntu 6.10以後，系統的默認shell /bin/sh被改成了dash。dash(the Debian Almquist shell) 是一個比bash小很多但仍兼容POSIX標準的shell，它佔用的磁盤空間更少，執行shell腳本比bash更快，依賴的庫文件更少，當然，在功能上無法與bash相比。dash來自於NetBSD版本的Almquist Shell(ash)。
> Ubuntu中將默認shell改為dash的主要原因是效率。由於Ubuntu啟動過程中需要啟動大量的shell腳本，為了優化啟動速度和資源使用情況，Ubuntu做了這樣的改動。
> ————————————————
> 版权声明：本文为CSDN博主「小猫的嵌入式」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/lucykingljj/article/details/48519069

sh 沒有對應 2 個中誇號，所以才會報錯

## 最佳執行 .sh 執行方法

只要 xxx.sh 上面有放`#! /bin/sh`
我們只要`./xxx.sh`就能順利執行了

就這們簡單!!!!

參考連結:
* [Linux shell腳本執行提示錯誤\[\[: not found - lucykingljj的專欄 - CSDN博客](https://blog.csdn.net/lucykingljj/article/details/48519069)
* [it.chinawin.net/os/article-8112.html](http://it.chinawin.net/os/article-8112.html)
* [#!/usr/bin/env bash和#!/bin/bash区别 - 小硕成长空间 - CSDN博客](https://blog.csdn.net/ziyoudeyun1993/article/details/65627518)
下面怕沒有時間寫，先預留連結
* [Store mysql result in a bash array variable - Stack Overflow](https://stackoverflow.com/questions/35274597/store-mysql-result-in-a-bash-array-variable)
* [shell script - How do I capture a MySQL result set in a bash array? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/446748/how-do-i-capture-a-mysql-result-set-in-a-bash-array)
* [How to Generate XML from XSD in Eclipse - JournalDev](https://www.journaldev.com/821/generate-xml-xsd-eclipse-java)
* [Generating Instance Documents From XML Schemas - Help | PhpStorm](https://www.jetbrains.com/help/phpstorm/generating-instance-document-from-xml-schema.html)