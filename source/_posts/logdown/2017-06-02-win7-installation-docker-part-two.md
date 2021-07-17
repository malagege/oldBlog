---
layout: post
title: '在Win7安裝Docker(下) - Window Volumes問題'
date: 2017-06-02 14:18
comments: true
categories: Docker
tags: [Volumes]
---
上篇Docker裝的還滿順利的，但RUN  https://github.com/bitnami/bitnami-docker-codeigniter
就發生了問題
```
ERROR: for s_myapp_1  Cannot create container for service myapp: invalid bind mo
unt spec "C:\\Users\\computer\\Desktop\\s:/app:rw": invalid volume specification
: 'C:\Users\computer\Desktop\s:/app:rw'

ERROR: for myapp  Cannot create container for service myapp: invalid bind mount
spec "C:\\Users\\computer\\Desktop\\s:/app:rw": invalid volume specification: 'C
:\Users\computer\Desktop\s:/app:rw'
ERROR: Encountered errors while bringing up the project.
```
<!--more-->

網路爬文有找到幾個方法，但都沒什麼用
http://qiita.com/ysti/items/aed9c766aed00fe731fc
http://www.cnblogs.com/anliven/p/6481516.html
https://xbug.lol/1341.html
http://yanjunbiao.me/2016/01/03/Volume-Mount-Issue-of-Docker-on-Windows/

`/c/Users/`和'`//C/Users/`都還不行

後來發現奇怪，怎麼MariaDB怎麼會有Volumes設定
```yml
version: '2'

services:
  mariadb:
    image: 'bitnami/mariadb:latest'
    labels:
      kompose.service.type: nodeport
    ports:
      - '3306:3306'
    volumes:
      - 'mariadb_data:/bitnami/mariadb'
    environment:
      # ALLOW_EMPTY_PASSWORD is recommended only for development.
      - ALLOW_EMPTY_PASSWORD=yes

volumes:
  mariadb_data:
driver: local
```

喔喔，原來可以加volumes

```yml
version: '2'
services:
  myapp:
    image: 'bitnami/codeigniter:latest'
    labels:
      kompose.service.type: nodeport
    ports:
      - '8000:8000'
    volumes:
      - 'myapp_data:/app'
    depends_on:
      - mariadb
  mariadb:
    image: 'bitnami/mariadb:latest'
    environment:
      - ALLOW_EMPTY_PASSWORD=yes
volumes:
  myapp_data:
    driver: local
```
就正常了

想說這樣應該能很順利，但...馬上就遇到Network問題
Docker port沒法轉到實體IP上面
我記得在VM跑ubuntu docker沒有這個問題
該不會又是Windows ....

想說這個能解決
https://blog.ephrain.net/virtualbox-%E4%BD%BF%E7%94%A8-port-forwarding-%E9%80%A3%E4%B8%8A-nat-%E6%A8%A1%E5%BC%8F%E4%B8%8B%E7%9A%84-vm/
遺憾還是不能....

[怎麼在win10 上安裝docker desktop ~ 迪貝之家](https://www.dbaid.tw/2021/07/win10-docker-desktop.html)