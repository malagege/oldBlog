---
title: 在 Raspberry Pi 建置 V2ray
date: 2021-03-28 16:35:29
tags: [v2ray]
categories: VPN
---

最近想讓樹莓派做 VPN
後來有找到這個
不同VPN使用方法，這個有點像做代理
後來選這個是因為做 https 可以用 traefik 做更新憑證
這樣我就可以省一步

<!--more-->

## 前言廢話

[GitHub - alphacodinghub/v2ray-nginx-docker: Easy Docker deployment for V2ray + WS +TLS with Nginx, free SSL and Traefik](https://github.com/alphacodinghub/v2ray-nginx-docker)
這邊有實例出來
但我在 build 出錯了...

查看實做一個V2ray 跑
[Docker 部署 V2Ray · V2Ray 配置指南|V2Ray 白话文教程](https://toutyrater.github.io/app/docker-deploy-v2ray.html)
好像沒什麼難度
不如自己試試看


意外這個在 Raspberry PI 也能跑
如果我猜的沒錯，這個掛在 Traefik 應該也能跑

```
docker run v2fly/v2fly-core
```

## 設定實做

參考設定:[WebSocket+TLS+Web · V2Ray 配置指南|V2Ray 白话文教程](https://toutyrater.github.io/advanced/wss_and_web.html)

建立 config.conf
```json
{
  "inbounds": [
    {
      "port": 10000,
      "listen":"0.0.0.0",//這邊原本是127.0.0.1，因為要讓traefik 做反向代理，所以設為0.0.0.0
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "b831381d-6324-4e53-ad4f-8cda48b30811", //建議更換 
            "alterId": 64
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "/ray"
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}

```


```bash
# 這邊可以測試看看能不能跑
 docker run --rm    -v `pwd`/config.json:/etc/v2ray/config.json v2fly/v2fly-core 
```

測試可以跑

我改寫程 docker compose 執行


```yml
version: "2"

services:
  v2ray_home:
    image: v2fly/v2fly-core
    environment:
      - TZ=Asia/Taipei
    expose:
      - "10000"
    restart: always
    networks:
      - traefik_network
    labels:
      - "traefik.http.routers.v2ray-https.rule=Host(`pekachu.xxxxxxxx.org`)"
      - "traefik.http.routers.v2ray-https.tls=true"

networks:
  traefik_network:
    external: true
```


client 設定

```json
{
  "inbounds": [
    {
      "port": 1080,
      "listen": "127.0.0.1",
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth",
        "udp": false
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "pekachu.xxxxxxxx.org",
            "port": 443,
            "users": [
              {
                "id": "b831381d-6324-4e53-ad4f-8cda48b30811", // 這邊id 需要跟 server 一樣，就等於是密碼
                "alterId": 64
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
        "wsSettings": {
          "path": "/ray"
        }
      }
    }
  ]
}
```

簡單備註
1. UUID 可更換用[Online UUID Generator Tool](https://www.uuidgenerator.net/)網站
2. address 需要放對印網址(DNS)

要連需要設定代理 sockv5 127.0.0.1 port: 10801(看v2ray client port設多少)
我是使用 v2rayN
相關設定可以看[跨越長城 V2Ray Client - 夜升筆談](https://blog.yosheng.tw/v2ray-client)

用瀏覽器執行

## 相關文章

[Shadowsocks vs V2Ray vs Trojan 都有什么区别？ - LightyearVPN](https://lightyearvpn.com/zh-TW/blog/shadowsocks-vs-v2ray-vs-trojan-for-gfw)

[V2Ray 記錄 | Flymia 凡事用心之事](https://ppundsh.github.io/posts/e457/)
裡面還滿多設定的，可以參考參考

[神一样的工具们 · Project V 官方网站](https://www.v2ray.com/awesome/tools.html)
裡面有線上config 產生器

[v2ray揭秘：速度最快的v2ray客户端 V2RayN V2RayW Clash Qv2ray Mellow （v2ray翻墙速度测试） - YouTube](https://www.youtube.com/watch?v=G2eXQrgq_n8)
Client 比較

[Clash for Windows配置V2ray教程 - V2ray科技](https://v2raytech.com/clash-for-windows-v2ray-tutorial/)
設個好像有做系統底層代理...不過我沒測試