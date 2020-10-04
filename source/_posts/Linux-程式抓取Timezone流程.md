---
title: Linux 程式抓取Timezone流程
date: 2020-08-31 21:44:34
tags: [linux, timezone]
categories: Linux
---

簡單小記

<!--more-->

Linux 程式抓取Timezone流程

​

[Linux —— 时间（tzselect、timedatactl命令，查看和修改时区，修改时区为东八区）_杰儿__er 的博客-CSDN博客](https://blog.csdn.net/weixin_42167759/article/details/90648225)

​

預設會抓Linux  TZ 環境變數，如果不存在，就會抓取/etc/localtime
注意這個不是單純txt，會用 linux link 方式去做連結的
所以可以ls - l /etc/localtime 看到指定的語系

1. echo $TZ;
2. ls -al /etc/localtime

還可以用指令看 `datetimectl` 可以看目前使用語系

就這麼簡單

​

​

其他小記
https://linuxhint.com/linux_date_command/
[HTTP日期/时间格式 - 簡書](https://www.jianshu.com/p/e0dd536dd3e4)
[Python 時間/時區處理 | CKPlus's Blog](https://ckplus.github.io/2016/08/20/python-timezone/)

