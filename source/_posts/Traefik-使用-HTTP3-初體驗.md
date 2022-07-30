---
title: Traefik 使用 HTTP3 初體驗
date: 2022-07-30 23:47:20
tags: [traefik, http]
categories: Linux
---

HTTP3 這麼潮當然要跟者流行阿，所以就實作。

<!--more-->


## 使用步驟

### 1. Traefik 設定


### 2. 防火牆(Docker 設定 443) UDP PORT

```yaml=
version: '3'

services:
  updateDuckDNSIP:
    # used to update duckdns
    # if you have a static IP, yu can comment this out
    # using busybox due to it having an image for almost every platform
    image: armhf/ubuntu
    command: /bin/sh -c "apt update -y && apt install curl -y && while true; do echo url=\"https://www.duckdns.org/update?domains=${DUCKDNS_SUBDOMAIN}&token=${DUCKDNS_TOKEN}\" | curl -k -o duck.log -K -; sleep 300; done"
    restart: always
    labels:
      - "traefik.enable=false"
  traefik:
    image: traefik
    restart: always
    command:
      - "--api.insecure=true"
      - "--providers.docker"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.web-secure.address=:443"
      #- "--entrypoints.web.http.redirections.entryPoint.to=web-secure"
      #- "--entrypoints.web.http.redirections.entryPoint.scheme=https"
      - "--certificatesresolvers.letsencrypt.acme.dnschallenge=true"
      - "--certificatesresolvers.letsencrypt.acme.email=s9714020@gmail.com"
      - "--certificatesresolvers.letsencrypt.acme.storage=acme.json"
      - "--certificatesresolvers.letsencrypt.acme.dnschallenge.provider=duckdns"
      - "--certificatesresolvers.letsencrypt.acme.dnschallenge.delaybeforecheck=0"
      - "--accesslog=true"
      - "--metrics.prometheus=true"
      - "--accesslog.format=json"
      - "--experimental.http3=true"
      - "--entrypoints.web-secure.http3=true"
      #- "--log.level=DEBUG"
      #- "--api.dashboard=true"
    ports:
      - "80:80"
      - "443:443/udp"
      - "443:443/tcp"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./acme.json:/acme.json
      #- ./traefik.yml:/etc/traefik/traefik.yml
    networks:
      - traefik_network
    environment:
      - DUCKDNS_TOKEN=${DUCKDNS_TOKEN}
    labels:
      - "traefik.enable=false"
      #- "traefik.http.routers.traefik.tls=true"
      - "traefik.http.routers.traefik.tls.certresolver=letsencrypt"
      - "traefik.http.routers.traefik.tls.domains[0].main=${DUCKDNS_SUBDOMAIN}.duckdns.org"
      - "traefik.http.routers.traefik.tls.domains[0].sans=*.${DUCKDNS_SUBDOMAIN}.duckdns.org,${DUCKDNS_SUBDOMAIN}.duckdns.org"
networks:
  traefik_network:
    external: true

```

```.env
#your duck dns token
DUCKDNS_TOKEN=2b42****

#your duck dns subdomain (not the full domain)
#use "test" not "test.duckdns.org
DUCKDNS_SUBDOMAIN=***
```

### 3. 確認是否有吃到 HTTP3


![](https://i.imgur.com/PhofIBf.png)


### 4. 清除瀏覽器 HSTS 紀錄

```
Chrome 的 HSTS 清除方法

    開啟 Chrome
    在網址列輸入：chrome://net-internals/#hsts
    先在上面 Query domain 輸入網域名稱查詢看看 # 可以略過此步驟
    用最下方的 Delete domain security policies：輸入「網域名稱」delete 即可
    或者：chrome://net-internals/#dns → clear host cache

Firefox 的 HSTS 清除方法

    開啟 Firefox
    方法1：
        Ctrl + Shift + H (Mac：Cmd + Shift + H)
        右上角「搜尋瀏覽紀錄」輸入網域名稱
        對要刪除的資料按右鍵 → 選擇「刪除與此網站有關的紀錄」
    方法2：
        偏好設定
        隱私權與安全性
        瀏覽紀錄
        設定：Firefox 將 "保留所有歷史紀錄"
        設定：清除瀏覽紀錄

```

參考:[如何清除 Firefox 與 Chrome 的 HSTS 設定 – Tsung's Blog](https://blog.longwin.com.tw/2018/06/how-to-clear-hsts-setting-browser-chrome-firefox-2018/)