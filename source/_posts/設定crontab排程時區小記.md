---
title: 設定crontab排程時區小記
date: 2019-05-16 22:24:28
tags:
categories:
---

最近因為要設定 crontab 時區
但主機時區不是台灣時間
但程式是台灣時間
但這時候控制排程可能就會有時間差的問題

<!--more-->

## 在 Linux 設定 方法

其實這時候很簡單，調整時區就能解決問題!!

Crontab 能不能調整時區?
答案可以：
[CentOS7 下 Crontab 时区问题 | MYF](https://menyifan.com/2016/11/08/crontab_timezone/)

> ntpdate -u tock.stdtime.gov.tw
> timedatectl set-timezone Asia/Taipei
> timedatectl set-local-rtc 0 # Set Real-Time Clock
> cp -pf /usr/share/zoneinfo/Asia/Taipei /etc/localtime

其他相關
[Linux 設定 NTP 同步系統時間，自動網路校時教學 - G. T. Wang](https://blog.gtwang.org/linux/linux-ntp-installation-and-configuration-tutorial/)
[使用 ntpd 來替換 ntpdate 以完成校時的工作 – SZ Lin & Embedded Linux](https://szlin.me/2016/07/19/%E4%BD%BF%E7%94%A8-ntpd-%E4%BE%86%E6%9B%BF%E6%8F%9B-ntpdate-%E4%BB%A5%E5%AE%8C%E6%88%90%E6%A0%A1%E6%99%82%E7%9A%84%E5%B7%A5%E4%BD%9C/)

可惜！目前專案不太希望我們動到主機時區 orz

## 設定 bash 環境變數

> TZ='Asia/Taipei'; export TZ

[crontab 时区以及 linux 时区设置-luosiyong](http://luosiyong.lofter.com/post/1cc7f64b_6cc5cfc)

這可以改變 Bash 變時區問題
但是 Crontab 還是不會有影響
[定时任务 cron 时区的问题](https://busy.org/@oflyhigh/cron#comments) {% asset_link 1.png 備份圖 %}

`/etc/init.d/cron`但這邊設定檔好像可以設定

**PS:還沒有驗證**

[6 Online Tools for Generating and Testing Cron Jobs for Linux](https://www.tecmint.com/online-cron-job-generator-and-tester-for-linux/)

其實，PHP CI 的 index.php 連線都有設定台灣時區，DB 連線也有設定
但之後程式可能需要有程式設定 crontab
目前有想到用程式減少相對時區
解決問題

小記 Laravel 設定時區[PHPer: Laravel 5 資料庫 timezone 設定](http://phpwrite.blogspot.com/2015/08/laravel-5-timezone.html)

```
'mysql' => [
    ...
    'options' => [
        PDO::MYSQL_ATTR_INIT_COMMAND => 'SET time_zone = "+08:00"'
    ],
    (or)
    'timezone' => '+08:00',
],
```

[Set default MySQL timezone on connect? | Laravel.io](https://laravel.io/forum/10-16-2015-set-default-mysql-timezone-on-connect)
[cron - How do you set the timezone for crontab? - Ask Ubuntu](https://askubuntu.com/questions/54364/how-do-you-set-the-timezone-for-crontab)
