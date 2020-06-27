---
title: php7安裝phpredis和mcrypt在docker方法
date: 2019-04-10 20:24:47
tags: [php, phpredis, mcrypt]
categories: PHP
---

最近有專案需要用到 phpredis,mcrypt
所以 php-fpm 需要安裝套件
有 google 找到方法
這邊簡單筆記

<!--more-->

## phpredis

phpredis 安裝很順利
`RUN pecl install redis && docker-php-ext-enable redis`

## mcrypt

在 PHP7.0 好像就不能使用，所以需要手動安裝

網路上看到

> PHP mcrypt extension 的安裝步驟
>
> ```
> sudo apt install re2c php7.2-dev libmcrypt-dev # php7.2-dev for phpize
> sudo pecl install mcrypt-1.0.1 # 產生在 /usr/lib/php/20170718/mcrypt.so
> ```
>
> PHP mcrypt 的設定步驟
>
> ```
> sudo vim /etc/php/7.2/mods-available/mcrypt.ini
> extension=mcrypt.so
> sudo phpenmod mcrypt
> ```
>
> 重新啟動 php / cgi / web 即可

這邊這一行`pecl install mcrypt-1.0.1`我在 php7.3 安裝不行
[PECL :: Package :: mcrypt](https://pecl.php.net/package/mcrypt)
不過看到 mcrypt 也有持續在更新，所以只要抓最新版 1.0.2 就可以安裝

這時候可以在 phpinfo 裡面看有沒有正常安裝

分享 Dockerfile

```dockerfile
FROM php:7.3-fpm

RUN apt-get update -y && apt-get install -y openssl zip unzip git libmcrypt-dev
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN docker-php-ext-install opcache bcmath pdo_mysql mysqli


RUN pecl install redis && docker-php-ext-enable redis


RUN pecl install mcrypt-1.0.2 && docker-php-ext-enable mcrypt


ARG DIR
WORKDIR $DIR

 RUN composer install

```

好奇 docker-php-ext-enable 有做什麼，所以有查了一下

## docker-php-ext-install

簡單來講就是安裝 PHP 內建現有套件

> php 目录下还准备好了 php 配置文件 php.ini 以及 php-fpm.conf，基础镜像我们选择的是 PHP 5.6-FPM，这同样是一个 Debian Jessie 镜像。官方比较亲切的在镜像内部准备了一个 docker-php-ext-install 指令，可以快速安装如 GD、PDO 等常用扩展。所有支持的扩展名称可以通过在容器内运行 docker-php-ext-install 获得。

[Docker 在 PHP 项目开发环境中的应用 - DockOne.io](http://dockone.io/article/481)
[docker-php-ext-install Reference](https://gist.github.com/giansalex/2776a4206666d940d014792ab4700d80)

## docker-php-ext-enable

原本我在[php7 - docker-php-ext-enable 的原理？ - SegmentFault 思否](https://segmentfault.com/q/1010000017449567)看不是很懂
後來[docker-php-ext-enable 可以開啟擴展的原理 | 一顆西瓜籽](https://huanghantao.github.io/2018/12/21/docker-php-ext-enable%E5%8F%AF%E4%BB%A5%E5%BC%80%E5%90%AF%E6%89%A9%E5%B1%95%E7%9A%84%E5%8E%9F%E7%90%86/)這篇看懂

> 開啟擴展的那一行是寫在文件/usr/local/etc/php/conf.d/docker-php-ext-擴展名.ini 這個配置文件裡面的。查看命令 php -i 的輸出 ... 煥然大悟。

看著這篇「恍然大悟」，我也跟者恍然大悟 XD

[docker-php-ext-enable 可以开启扩展的原理 | 一颗西瓜籽](https://huanghantao.github.io/2018/12/21/docker-php-ext-enable%E5%8F%AF%E4%BB%A5%E5%BC%80%E5%90%AF%E6%89%A9%E5%B1%95%E7%9A%84%E5%8E%9F%E7%90%86/)

最後[windows 下 php 无法使用 mcrypt 的解决 - xieye - ITeye 博客](https://xieye.iteye.com/blog/802509)，因為不知道 window 要怎麼安裝 mcrypt
剛好有翻到這篇，不過我沒有嘗試。

參考來源:

- [How to install php-redis extension using the official PHP Docker image approach? - Stack Overflow](https://stackoverflow.com/questions/31369867/how-to-install-php-redis-extension-using-the-official-php-docker-image-approach)
- [How to install mcrypt in php7.2 / php7.3 | Lukáš Mešťan](https://lukasmestan.com/install-mcrypt-extension-in-php7-2/)
- [docker php7.2 not installing mcrypt · Issue #541 · docker-library/php](https://github.com/docker-library/php/issues/541)
- [windows 下 php 无法使用 mcrypt 的解决 - xieye - ITeye 博客](https://xieye.iteye.com/blog/802509)
- [php7.1 加密解密 openssl_encrypt 替代 mcrypt - 简书](https://www.jianshu.com/p/ad88f1b8b2df)

最近看到有人分享 phpenv.cn
發現裡面有整合 nginx 和 redis
為什麼會寫在這邊呢 怕忘記寫...
