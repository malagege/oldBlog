---
title: PHP-FPM 打開 error log 方法
date: 2019-05-22 22:08:34
tags: [php, php-fpm]
categories: PHP
---

最近用 docker 使用 portainer 看 log
卻沒有看到我想要的程式錯誤資訊
查了一下 PHP-FPM 預設沒有打開

<!--more-->

## PHP-FPM 調整設定

/usr/local/etc/php-fpm.d/www.conf

> 找到 catch_workers_output 設定，改為 yes
> ; Redirect worker stdout and stderr into main error log. If not set, stdout and
> ; stderr will be redirected to /dev/null according to FastCGI specs.
> ; Default Value: no
> catch_workers_output = yes

[XYZ 的筆記本: PHP FPM 錯誤記錄不會寫到 error log ?](https://xyz.cinc.biz/2016/07/php-fpm-error-log.html)

> 1. 修改 php-fpm.conf 文件，添加（或修改）如下配置：
>
> [global]
> error_log = log/error_log <-- 這個我沒設定，只設定 catch_workers_output = yes
>
> [www]
> catch_workers_output = yes
>
> 2.  修改 php.ini 文件，添加（或修改）如下配置：
>
> log_errors = On
> error_log = "/usr/local/lnmp/php/var/log/error_log"
> error_reporting=E_ALL&~E_NOTICE
>
> 3.  重启 php-fpm

參考來源:
[开启 php-fpm 错误日志](https://gist.github.com/BiosSun/7244141)

## 設定 PHP Log 小方法

在用 docker PHP-FPM 路徑在/usr/local/etc/php-fpm.d/www.conf
因為我 docker 沒安裝 vi, nano
所以我想用 `sed` 修改檔案

> 這時候要把這個文件的 phobia 通通改成 phobe：
>
>     sed -i -e 's/phobia/phobe/' phobia.txt
>     sed -i -e 's/phobia/phobe/g' phobia.txt
>     sed -i .bak -e 's/phobia/phobe/g' phobia.txt
>
> 注意-i 代表 Edit file in-place，也就是直接編輯檔案的意思
>
> 第 2 個指令多了一個 g, 代表同一行裡面如果出現多次則通通修改；第 1 個指令只會改掉每一行裡面第一個遇到的 pattern。在 Mac 裡面-i 的行為跟在 Linux 下會不一樣，像是第 3 行的
> 指令在 Linux 下就只會直接編輯檔案；但在 Mac 下卻會被備份一個檔案後面接著-e
>
> 第 3 行指令裡面的-i 後面接.bak 意思為，把原始的檔案多加.bak 存成備份。
> [阿就操場啊~: sed 直接編輯文件，做文字取代](https://2formosa.blogspot.com/2018/03/sed-edit-a-file-directly.html)

所以簡單使用

```bash
sed -i 's/;catch_workers_output = yes/catch_workers_output = yes/g' www.conf
```

相關連結
[Sed 的常用命令语法备忘【转】 | Henter](https://henter.me/post/sed-notes.html)
