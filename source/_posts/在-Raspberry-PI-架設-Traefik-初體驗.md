---
title: 在 Raspberry PI 架設 Traefik 初體驗
date: 2020-10-13 23:22:45
tags: [traefik]
categories: Linux
---

最近在樹梅派使用 Docker 服務多
常常設完一個服務，我就要去Virtual Server 設定一個 port
最後想到可以架一個 Server 做反向代理
這時候我想到 nginx 和 caddy
caddy 又可以幫我換 letsencrypt 憑證
最後看到這篇[用 Traefik 搭配 Docker 快速架設服務 | 小惡魔 - 電腦技術 - 工作筆記 - AppleBOY](https://blog.wu-boy.com/2019/01/deploy-service-using-traefik-and-docker/)

> 我要介紹另外一套工具叫 Traefik，這一套也是用 Go 語言開發，而我推薦這套的原因是，此套可以跟 Docker 很深度的結合，只要服務跑在 Docker 上面，Traefik 都可以自動偵測到，並且套用設定。透過底下的範例讓 Traefik 串接後端兩個服務，分別是 domain1.com 及 domain2.com。

好像能試試看喔

<!--more-->

## 事前準備

### docker-compose
我發現我樹梅派沒安裝 docker-compose

```bash
sudo pip3 install docker-compose
```

安裝就這麼簡單

### 修改舊的 dockerfile

發現有人做過
[Docker 与 Traefik 助微服务腾飞 - 后端](https://juejin.im/entry/6844903458986786830)
不過版本是1.3.8
現在出到 2 了
想使用新版本
看一下舊版的 dockerfile 
只是下載直接執行
[rpi-traefik/Dockerfile at master · hypriot/rpi-traefik](https://github.com/hypriot/rpi-traefik/blob/master/Dockerfile)

~~PS: Dockerfile ADD 可以放 http 以外，他可以放 gz 連結，會自動解壓縮~~
[Dockerfile COPY 和 ADD 指令區別和使用規則 - Lishude's Web Note](https://islishude.github.io/blog/2019/06/29/docker/Dockerfile-COPY-%E5%92%8C-ADD-%E6%8C%87%E4%BB%A4%E5%8C%BA%E5%88%AB%E5%92%8C%E4%BD%BF%E7%94%A8%E8%A7%84%E5%88%99/)

> 上面的命令會導致foo.tar.gz歸檔文件的內容被解壓到容器的/ tmp目錄中。 有趣的是，URL下載和解壓功能不能一起使用。任何通過URL複製的壓縮文件都不會自動解壓縮。
[Dockerfile:ADD VS COPY](https://juejin.im/post/6844903574539862030)

> ADD的另外一個特性是有能力自動解壓文件。如果<src>參數是一個可識別的壓縮格式（tar, gzip, bzip2, etc）的本地文件（所以實現不了同時下載並解壓），就會被解壓到指定容器文件系統的路徑<dest>。
ADD /foo.tar.gz /tmp/
上述指令會使foo.tar.gz壓縮文件解壓到容器的/tmp目錄。
URL下載和解壓特性不能一起使用。任何壓縮文件通過URL拷貝，都不會自動解壓。
目前ADD指令有點讓人迷惑，有時候解壓文件，有時候不解壓文件，如果你想拷貝一個壓縮文件，你會以為地解壓。如果文件是某種不能識別的壓縮文件，如果你想解壓，你又會意外地複製它。
[docker Dockerfile指令ADD和COPY的區別，添加目錄方法 - 大自然的流風 - 博客園](https://www.cnblogs.com/zdz8207/p/linux-docker-add-copy.html)


```dockerfile
FROM hypriot/rpi-alpine:3.6
ADD https://github.com/traefik/traefik/releases/download/v2.3.1/traefik_v2.3.1_linux_armv7.tar.gz /traefik.tar.gz
RUN tar zxvf traefik.tar.gz
RUN apk upgrade --no-cache &&\
    apk add --no-cache ca-certificates &&\
    chmod +x /traefik
EXPOSE 80 8080 443
ENTRYPOINT ["/traefik"]
```

做到後面，我發現有現成的 Image 
[arm64v8/traefik - Docker Hub](https://hub.docker.com/r/arm64v8/traefik)

## Raspberry PI 安裝 traefik

docker build . -t traefik

### 建立 network

docker network create traefik_network


### 建置 docker-compose

```yml
version: '3'

services:
  traefik:
    image: traefik
    command: --api.insecure=true --providers.docker
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - traefik_network
networks:
  traefik_network:
    external: true

```

### 建置 whoami

```yml
version: "2"

services:
  whoami:
    image: hypriot/rpi-whoami
    networks:
        - traefik_network
    environment:
      - constraint:hypriot.hierarchy!=leader
      - PORT=8000
    labels:
      - "traefik.backend=whoami"
#      - "traefik.frontend.rule=Host:whoami.docker.localhost"
#2.3.1 上面設定好像會無效
      - "traefik.http.routers.router0.rule=Host(`whoami.localhost`) || PathPrefix(`/whoami`)"
      - "traefik.port=8000"

networks:
  traefik_network:
    external: true
```

## 測試方法

### 方法1:修改hosts檔案

/etc/hosts

127.0.0.1    whoami.localhost

### 方法2: curl -H xxx

curl -H Host:whoami.localhost http://192.168.1.1/

[Docker 与 Traefik 助微服务腾飞 - 后端](https://juejin.im/entry/6844903458986786830)
裡面適用這個方法

推薦使用這個，這樣就不用做多餘的設定

舊版本差異
[Traefik v1 to v2 - Traefik](https://doc.traefik.io/traefik/migration/v1-to-v2/)



[traefik 简易入门 | 个人服务器运维指南 | 山月行](https://shanyue.tech/op/traefik.html#%E5%BF%AB%E9%80%9F%E5%BC%80%E5%A7%8B)
[arm32v6/traefik - Docker Hub](https://hub.docker.com/r/arm32v6/traefik/)
[Traefik](https://doc.traefik.io/traefik/)
[用 Traefik 搭配 Docker 快速架設服務 | 小惡魔 - 電腦技術 - 工作筆記 - AppleBOY](https://blog.wu-boy.com/2019/01/deploy-service-using-traefik-and-docker/)
[10 分鐘內用 Traefik 架設 Drone 搭配 GitHub 服務 | 小惡魔 - 電腦技術 - 工作筆記 - AppleBOY](https://blog.wu-boy.com/2019/03/setup-traefik-with-drone-ci-cd-in-ten-minutes/)
[个人网站实现方案更新：Traefik 反向代理、与 NAS 共用 Wildcard 证书等 – Blanboom](https://blanboom.org/2019/traefik-and-wildcard-cert-on-my-website/)



> 當我們訪問集群內部服務，如數據庫，緩存，traefik Dashboard，gitlab 時，如果直接暴露在公網中，則會造成很大安全隱患，此時我們應該如何處理呢？

[traefik 簡易入門 | 個人服務器運維指南 | 山月行](https://shanyue.tech/op/traefik.html#%E6%80%BB%E7%BB%93)

[一文搞懂 Traefik2.1 的使用_River's Site - MdEditor](https://www.mdeditor.tw/pl/p8UD/zh-tw)
[使用 Docker 和 Traefik 搭建 GitLab （前篇） - 知乎](https://zhuanlan.zhihu.com/p/61967315)
[在Docker Swarm上部署Traefik2.x - 简书](https://www.jianshu.com/p/3ea76663243b)


## traefik 綁定 lets wildcard

wildcard 只能用 dns chang


### 註銷舊有憑證方法

沒用的跳過
certbot revoke --cert-path /etc/letsencrypt/archive/${YOUR_DOMAIN}/cert1.pem

不過後來沒是成功
我先把nextcloud 綁下來
感覺不能直接這樣做??
後來想說直接用新的 url

### 設定

#### acme.json

```bash
# 在docker-compose.yml 路徑
touch acme.json && chmod 600 acme.json
```

#### 設定 traefik docker-compose

參考這個[traefik_duckdns/docker-compose.yml at master · KnicKnic/traefik_duckdns](https://github.com/KnicKnic/traefik_duckdns/blob/master/docker-compose.yml)

對應我的 docker-compose 做修改

```env .env
#your duck dns token
DUCKDNS_TOKEN=

#your duck dns subdomain (not the full domain)
#use "test" not "test.duckdns.org
DUCKDNS_SUBDOMAIN=

```


```yml
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
    command:
      - "--api.insecure=true"
      - "--providers.docker"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.web-secure.address=:443"
      - "--certificatesresolvers.letsencrypt.acme.dnschallenge=true"
      - "--certificatesresolvers.letsencrypt.acme.email=xxxxx@gmail.com"
      - "--certificatesresolvers.letsencrypt.acme.storage=acme.json"
      - "--certificatesresolvers.letsencrypt.acme.dnschallenge.provider=duckdns"
      - "--certificatesresolvers.letsencrypt.acme.dnschallenge.delaybeforecheck=0"
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./acme.json:/acme.json
    networks:
      - traefik_network
    environment:
      - DUCKDNS_TOKEN=${DUCKDNS_TOKEN}
    labels:
      - "traefik.enable=false"
      - "traefik.http.routers.traefik.tls=true"
      - "traefik.http.routers.traefik.tls.certresolver=letsencrypt"
      - "traefik.http.routers.traefik.tls.domains[0].main=${DUCKDNS_SUBDOMAIN}.duckdns.org"
      - "traefik.http.routers.traefik.tls.domains[0].sans=*.${DUCKDNS_SUBDOMAIN}.duckdns.org,${DUCKDNS_SUBDOMAIN}.duckdns.org"
networks:
  traefik_network:
    external: true
```

這邊有個小技巧，使用搭配 .env
可以用 docker-compose config 看看有沒有設定到

certresolver 的 letsencrypt 是看自己命名的名子certificatesresolvers.`letsencrypt`.acme.dnschallenge

whoami

```bash
version: "2"

services:
  whoami:
    image: hypriot/rpi-whoami
    networks:
        - traefik_network
    environment:
      - constraint:hypriot.hierarchy!=leader
      - PORT=8000
    labels:
      - "traefik.backend=whoami"
      - "traefik.http.routers.whoami.rule=Host(`whoami.xxxxx`) || PathPrefix(`/whoami`)"
      - "traefik.http.routers.whoami.tls=true"
      - "traefik.http.services.whoami.loadbalancer.server.port=8000"

networks:
  traefik_network:
    external: true
```

### 問題整理

####  port is missing

traefik_1          | time="2020-10-18T12:15:24Z" level=error msg="service \"seafile\" error: port is missing" providerName=docker container=seafile-7d40e43962df0ec1ca3af66d553c7ae5b81364497bb5f4f2c77597c95d210370

~~要開隨一 port? 就能解決~~

原本是用 traefik.http.services.seafile.loadbalancer.server.port=80
~~**但可能也不是應該用這種方式** 有更好方式再補充~~
去控制 port 不是用 traefik.port


查看官方文件[Docker - Traefik](https://doc.traefik.io/traefik/providers/docker/)

> Ports detection works as follows:
>
>    If a container exposes only one port, then Traefik uses this port for private communication.
>    If a container exposes multiple ports, or does not expose any port, then you must manually specify which port Traefik should use for communication by using the label traefik.http.services._service_name_.loadbalancer.server.port (Read more on this label in the dedicated section in routing).

exposes 就是 docker 對外設定的 port

```dockerfile
EXPOSE <port> [<port>/<protocol>...]
```

難怪...，nextcloud seafile 都需要設定 traefik.http.services.seafile.loadbalancer.server.port=80



#### https 導向問題

進去 nextcloud https 網站時候
會發現進不去

內層服務強制 https 取消

#### Container 取消關聯 

[Backend: Docker - Træfik](https://doc.traefik.io/traefik/v1.4/configuration/backends/docker/)

docker run -d -p 8000:8000 -p 9000:9000 -l "traefik.enable=false" --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:alpine@sha256:f13f14be723c848e119fa8d85c932dbcc0cbe3cfe09e784ceb09adefa248ccd1

#### HTTPS redirection (80 to 443)

待確認

--entrypoints.web.address=:80
--entrypoints.web.http.redirections.entryPoint.to=web-secure
--entrypoints.web.http.redirections.entryPoint.scheme=https
--entrypoints.web-secure.address=:443


這邊要注意 entrypoints.`web-secure`.address 裡面的命名

#### 同時使用 http 和 https 反向代理

用 nginx , apache 相關反向代理
相信不用處理是否要 http , https 
預設就可以了

但我發現這邊不行
後來看了官網文件[Routers - Traefik](https://doc.traefik.io/traefik/routing/routers/#general)

> Routers for HTTP & HTTPS
> 
> If you need to define the same route for both HTTP and HTTPS requests, you will need to define two different routers: one with the tls section, one without.

```toml
## Dynamic configuration
[http.routers]
  [http.routers.my-https-router]
    rule = "Host(`foo-domain`) && Path(`/foo-path/`)"
    service = "service-id"
    # will terminate the TLS request
    [http.routers.my-https-router.tls]

  [http.routers.my-http-router]
    rule = "Host(`foo-domain`) && Path(`/foo-path/`)"
    service = "service-id"
```

簡單說就是要寫兩個 router 

Docker Label 設定方法

```yml
    labels:
      - "traefik.backend=whoami"
      - "traefik.http.routers.whoami.rule=Host(`whoami.twlife.duckdns.org`) || PathPrefix(`/whoami`)"
      - "traefik.port=8000"
      #- "traefik.protocol=http" default http
      - "traefik.http.routers.whoami-https.rule=Host(`whoami.twlife.duckdns.org`)"
      - "traefik.http.routers.whoami-https.tls=true"

```


#### traefik docker 設定小記

這邊主要是 traefik 的 docker 設定

提幾個我覺得~~驚奇~~重要

1. endpoint 指定 docker socket file 路徑[Docker - Traefik](https://doc.traefik.io/traefik/providers/docker/#endpoint)

2. exposedByDefault

單一 port 可以不用設定連到是因為這個關係
預設true

3. defaultRule [Docker - Traefik](https://doc.traefik.io/traefik/providers/docker/#defaultrule)

Default=Host(`{{ normalize .Name }}`) 

建立 docker 最自動產生 docker router 名稱原來是這邊來的


#### 設定 BaseAuth

##### 產生 帳號/密碼

# Note: when used in docker-compose.yml all dollar signs in the hash need to be doubled for escaping.
# To create user:password pair, it's possible to use this command:
# echo $(htpasswd -nb user password) | sed -e s/\\$/\\$\\$/g

- [BasicAuth - Traefik](https://doc.traefik.io/traefik/middlewares/basicauth/)
- [在线 htpasswd 生成器](https://tool.oschina.net/htpasswd)

```
  - "traefik.http.routers.router1.middlewares=test-auth@docker"
  - "traefik.http.middlewares.test-auth.basicauth.users=test:$$apr1$$wB4YY5ua$$EocdCl08mI3Pk.6nItqZC/,test2:$$apr1$$2n2YpbTP$$c.FBjTJcfU0FkFZ4u912G/"
```

##### 從檔案讀取帳號密碼

```yml
labels:
  - "traefik.http.middlewares.test-auth.basicauth.usersfile=/path/to/my/usersfile"
```

```
test:$apr1$H6uskkkW$IgXLP6ewTrSuBkTrqE8wj/
test2:$apr1$d9hr9HBB$4HxwgUir3HP4EsggP/QNo0
```




#### openvpn 憑證

PiVPN 

[OpenVPN Access Server & Let’s Encrypt | Medium](https://medium.com/@ncsayeed/guide-openvpn-access-server-lets-encrypt-certbot-d8b65e8fdef)
[SnowMB/traefik-certificate-extractor: Tool to extract Let's Encrypt certificates from Traefik's ACME storage file.](https://github.com/SnowMB/traefik-certificate-extractor)
[Store traefik let's encrypt certificates not as json - Stack Overflow](https://stackoverflow.com/questions/47218529/store-traefik-lets-encrypt-certificates-not-as-json)
[OpenVPN Access Server Letsencrypt](https://gist.github.com/HighMacGuy/3cf42ce21d3bd923f2440f9938e2f664)
[ldez/traefik-certs-dumper: Dump ACME data from Traefik to certificates](https://github.com/ldez/traefik-certs-dumper)
[OpenVPN with Traefik 2.2 using UDP · Jurian Sluiman](https://juriansluiman.nl/posts/openvpn-with-traefik-2.2/)


* [Traefik, OpenVPN and Direct Container Connections on Docker Swarm](https://alex.thom.ae/2020/09/19/traefik-openvpn-docker-swarm/)
* [authelia/authelia: The Single Sign-On Multi-Factor portal for web apps](https://github.com/authelia/authelia)
* [Traefik v2.0 with Cloudflare Wildcard and OpenVPN](https://gist.github.com/darth-veitcher/93acda9617bab3e1de0264cebf4637fc)
* [当我有一台服务器时我做了什么 - 山月行的个人空间 - OSCHINA - 中文开源技术交流社区](https://my.oschina.net/u/4592353/blog/4434556)
* [Linux SSH 加上 Google 兩階段認證服務檢核 | Tsung's Blog](https://blog.longwin.com.tw/2014/10/ssh-google-2-factor-authentication-2014/)
* [Ubuntu 16.04 安裝 OpenVPN 加上 MFA 驗證 – Mr. 沙先生](https://shazi.info/ubuntu-16-04-%E5%AE%89%E8%A3%9D-openvpn-%E5%8A%A0%E4%B8%8A-mfa-%E9%A9%97%E8%AD%89/)
* [終於解決 Linux 上 VPN 連線 SSL 連線錯誤的問題 | Tsai I-ta's Blog](https://tsai.it/archives/2018/08/%E7%B5%82%E6%96%BC%E8%A7%A3%E6%B1%BA-linux-%E4%B8%8A-vpn-%E9%80%A3%E7%B7%9A-ssl-%E9%80%A3%E7%B7%9A%E9%8C%AF%E8%AA%A4%E7%9A%84%E5%95%8F%E9%A1%8C/)

#### access log

cli

--accesslog=true

[Access Logs - Traefik](https://doc.traefik.io/traefik/observability/access-logs/)

#### log

[一文搞懂Traefik的可观测性](https://juejin.im/post/6844904117429600270)
