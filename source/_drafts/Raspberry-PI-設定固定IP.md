---
title: Raspberry PI 設定固定IP
comments: true
categories: Linux
tags: [linux]
---
https://sites.google.com/site/raspberypishare0918/home/di-yi-ci-qi-dong/1-6-you-xian-huo-wu-xian-dedhcp

在更改ip后，如果没有重启服务，实际上是不发生变化的（我以前的方法是重启电脑，够笨的）
重启网络服务的命令是service network restart
或者/etc/init.d/network restart