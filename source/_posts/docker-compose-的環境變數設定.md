---
title: docker-compose 的環境變數設定
date: 2021-06-02 20:46:23
tags: [docker]
categories: Docker
---

[Docker Compose 引用环境变量 - sparkdev - 博客园](https://www.cnblogs.com/sparkdev/p/9826520.html)

之前用 docker-compose 都不知道可以抓環境變數
甚至 .env 也可以抓到
某天看到這個方法
今天來整理一下，不確定是哪個版本才有這個功能

<!--more-->

## Bash 環境變數

```bash=
export IMAGETAG=httpd
```

windows posershell 不知道要怎麼抓
但先不研究



```yaml=
version: '3'

services:
  web:
    image: ${IMAGETAG}
    ports:
     - "80:80"
  redis:
    image: "redis:alpine"


```


### 預設環境變數

重新開新的bash

```yaml=
version: '3'
services:
  web:
    image: ${IMAGETAG:-nginx}
    ports:
     - "80:80"
  redis:
    image: "redis:alpine"
```


### 環境變數傳給容器

```yaml=
web:
  environment:
    DEBUG: 1
```
同等於 
 docker run -e DEBUG=1 ubuntu bash


```bash=
docker run -e DEBUG=1 ubuntu bash

echo $DEBUG
#1
```

沒有設定 1 會為 null
請參考如下

```yaml=
web:
  environment:
    DEBUG: 
```
![](https://i.imgur.com/oFP350e.png)

再 Linux 設定 DEBUG 環境變數為 1


![](https://i.imgur.com/vNoQqee.png)

##  .env

參考: [Environment variables in Compose | Docker Documentation](https://docs.docker.com/compose/environment-variables/)
```
$ cat .env
TAG=v1.5

$ cat docker-compose.yml
version: '3'
services:
  web:
    image: "webapp:${TAG}"
$ docker-compose config

version: '3'
services:
  web:
    image: 'webapp:v1.5'
```



## 設定檔案為容器多個環境變數

```
$ cat .env
TAG=v1.5

$ cat ./config/.env.dev
TAG=v1.6


$ cat docker-compose.yml
version: '3'
services:
  web:
    image: "webapp:${TAG}"


$ docker-compose config 
version: '3'
services:
  web:
    image: 'webapp:v1.5'
    
$ docker-compose --env-file ./config/.env.dev config 
version: '3'
services:
  web:
    image: 'webapp:v1.6'
```

最下面同等於 

```bash
docker run --env-file=./config/.env.dev
```


## 環境變數使用順序


1. Compose file
2. Shell environment variables
3. Environment file
4. Dockerfile
5. Variable is not defined



> 首先，在 docker-compose.yml 文件中直接設置的值優先級是最高的。
然後是在當前 shell 中 export 的環境變量值。
接下來是在環境變量文件中定義的值。
再接下來是在 Dockerfile 中定義的值。
最後還沒有找到相關的環境變量就認為該環境變量沒有被定義。


## 依照環境變數建置測試/正式環境

參考: [Docker Compose 引用環境變量 - sparkdev - 博客園](https://www.cnblogs.com/sparkdev/p/9826520.html)

test.sh
```shell=
#!/bin/bash
# define env var default value.
export IMAGETAG=web:v1
export APPNAME=HelloWorld
export AUTHOR=Nick Li
export VERSION=1.0
```

prod.sh
```shell=
#!/bin/bash
# define env var default value.
export IMAGETAG=webpord:v1
export APPNAME=HelloWorldProd
export AUTHOR=Nick Li
export VERSION=1.0LTS
```

可依照 shell 環境變數建立不同環境
還算滿方便的方法

```shell=
 source test.sh
$ docker-compose config


$ source prod.sh
$ docker-compose config
```