---
title: 簡單用 Docker 部屬前端程式 nginx
date: 2021-07-24 14:56:43
tags: [docker,nginx,fronted]
categories: Docker
---

# 簡單用 Docker 部屬前端程式 nginx

之前這篇[Vue Router 4 學習筆記 | 程式狂想筆記](https://malagege.github.io/blog/2021/06/27/Vue-Router-4-%E5%AD%B8%E7%BF%92%E7%AD%86%E8%A8%98/#%E4%BC%BA%E6%9C%8D%E5%99%A8%E9%85%8D%E7%BD%AE)有記錄到要怎麼使用 nginx，今天就簡單實作看看。

<!--more-->

## 取得 nginx.conf 設定檔

可參考:[Nginx - Official Image | Docker Hub](https://hub.docker.com/_/nginx)
```bash=
docker run --name tmp-nginx-container -d nginx
docker cp tmp-nginx-container:/etc/nginx/nginx.conf /host/path/nginx.conf
docker rm -f tmp-nginx-container
```

我以前有有寫過[抓出 Docker 裡面檔案設定檔出來 | 程式狂想筆記](https://malagege.github.io/blog/2021/04/15/%E6%8A%93%E5%87%BA-Docker-%E8%A3%A1%E9%9D%A2%E6%AA%94%E6%A1%88%E8%A8%AD%E5%AE%9A%E6%AA%94%E5%87%BA%E4%BE%86/)，但久久沒用就忘記差不多...


## 前端設定

加在 nginx.conf 上面。

```
location / {
  try_files $uri $uri/ /index.html;
}

```

要補上 `server` ，要放在 http 裡面。
![](https://i.imgur.com/yWFaaCs.png)

```
    server{
      listen 80;
      location / {
        root /usr/share/nginx/html;
        try_files $uri $uri/ /index.html;
      }
    }


```

參考:[不同的历史模式 | Vue Router](https://next.router.vuejs.org/zh/guide/essentials/history-mode.html#apache)


## Docker volume 掛載設定

```bash=
docker run --rm -v $(pwd)/dist:/usr/share/nginx/html:ro -v $(pwd)/nginx.conf:/etc/nginx/nginx.conf:ro -p 3355:80 nginx

```

可以跑的話再設定

[nginx 基礎設定教學](https://blog.hellojcc.tw/nginx-beginner-tutorial/)

## 設定 docker-compose

加上 traefik 設定

```yaml=
version: "2"

services:
  strTemplateTest:
    image: nginx
    volumes:
        - ./dist/:/usr/share/nginx/html
        - ./nginx.conf:/etc/nginx/nginx.conf
        - /etc/localtime:/etc/localtime:ro
    networks:
        - traefik_network
    restart: always

    labels:
      - "traefik.http.routers.strTemplateTest.rule=Host(`strTemplateTest.xxxx.org`)"
      - "traefik.http.routers.strTemplateTest.tls=true"
networks:
  traefik_network:
    external: true

```


## 之後...

之後研究 Drone CI 會研究怎麼 build dockerfile，然後自動化部屬。之後再說，顆顆。