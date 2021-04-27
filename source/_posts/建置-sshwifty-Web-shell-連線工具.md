---
title: 建置 sshwifty (Web shell) 連線工具
date: 2021-03-28 15:51:06
tags: [ssh,shell]
categories: Linux
---

最近有遠端需求
所以在找類似這個工具

<!--more-->

## sshwifty

參考網路上範例修改成 traefik 

```bash
version: "2"

services:
  sshwifty_home:
    image: niruix/sshwifty
#    container_name: sshwifty_home
    environment:
      - TZ=Asia/Taipei
    expose:
      - "8182"
    restart: always
    networks:
      - traefik_network
    labels:
      #- "traefik.http.routers.sshwifty.rule=Host(`sshwifty.xxxxxxxx.org`) || PathPrefix(`/sshwifty`)"
      - "traefik.port=8000"
      - "traefik.protocol=http"
      - "traefik.http.routers.sshwifty-https.rule=Host(`sshwifty.xxxxxxxx.org`)"
      - "traefik.http.routers.sshwifty-https.tls=true"
      - "traefik.http.middlewares.sshwifty-auth.basicauth.users=user:(htppaswd encode password)" # 記得做密碼調整
      #- "traefik.http.routers.sshwifty.middlewares=sshwifty-auth@docker"
      - "traefik.http.routers.sshwifty-https.middlewares=sshwifty-auth@docker"

networks:
  traefik_network:
    external: true
```

[分享一个超实用的 Web 版 SSH 工具 Sshwifty - 奇妙的 Linux 世界](https://www.hi-linux.com/posts/2389.html)
[Sshwifty 一款网页端SSH客户端程序 | reizhi](https://roov.org/2020/10/sshwifty-web-ssh-client/)

## 其他相關 web ssh 工具



### Web SSH 工具整理

- GateOne
- Sshwifty
- wssh
- shellinabox
- web-shell
- qweb


[开源web终端ssh解决方案_程序狗的成长之路-CSDN博客](https://blog.csdn.net/shenwansangz/article/details/52711577)
[【webssh】网页上的SSH终端 - K.Takanashi - 博客园](https://www.cnblogs.com/franknihao/p/8963634.html)



https://gist.github.com/vitoo/8da59382a1565282ed55e78637a6a2f5