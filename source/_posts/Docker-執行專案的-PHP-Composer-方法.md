---
title: Docker 執行專案的 PHP Composer 方法
date: 2019-11-12 21:17:33
tags:
categories:
---

最近找了一下方法
記錄一下


<!--more-->

這邊說明一下，最近把公司舊程式 Docker 化
再一些程式初始化在想要怎麼用
**一般不需要這樣做**，再有 CI /CD 情況下可以直接用 dockerfile 去做部屬，程式就不需要做掛載動作
由於測試環境是放在 ftp 上面做 volume ，所以我沒有在 docker-compose 在架設初始化自己先做
但網路資源很少，還是紀錄一下

## docker-compose 方法 1

```yml
version: '3'
services:
  vvv:
    build: ./xxx
    volumes:
    - ./ssss:/ooooo
    command: php  xxxx
    depends_on:
      - composer
  composer:
    restart: 'no'
    image: composer
    command: install
    volumes:
      - ./ssss:/ooooo
```

## docker-compose 方法 2

```yml
version: '3'
services:
  vvv:
    build: ./xxx
    volumes:
      - ./ssss:/ooooo
    command: bash -c "cd {composer_path} && composer install  && php  xxxxx"
```


