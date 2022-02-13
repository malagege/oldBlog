---
title: Authelia 設定和 實作 2FA 登入驗證
date: 2022-02-13 23:38:28
tags: [2fa,sso, authelia]
categories: 程式心法
---

我家本身有用 Traefik，想讓 traefik 可以做 SSO 控制。

<!--more-->

## 官方簡單實作

需使用root帳號，參考[Deployment - Lite - Authelia](https://www.authelia.com/docs/deployment/deployment-lite.html)。

```bash=
git clone https://github.com/authelia/authelia.git
cd authelia/examples/compose/local #  這邊我是使用local
git checkout $(git describe --tags `git rev-list --tags --max-count=1`)# 或者用master也可以
sudo ./setup.sh # 他會加 hostname  特定網域如: *.example.com
```

記得改 configuration.yml 裡面 `encryption_key`，參考:[SQLite - Authelia](https://www.authelia.com/docs/configuration/storage/sqlite.html#encryption_key)

啟動可以會遇到全權限問題，可以參考: **docker 設定 user 文章**

**我的設定**


### 設定 2FA

設定上非常簡單，這邊小記重點，下面有範例看。

#### 測試用 mail

```yaml=
notifier:
  disable_startup_check: false
  filesystem:
    filename: /config/notification.txt

```

可以 less 查看 mail 內容。

[Notifier - Authelia](https://www.authelia.com/docs/configuration/notifier/)

#### 產生 users_database.yml 的 hash密碼值

```bash=
$(sudo docker run authelia/authelia authelia hash-password __你要加密的密碼__ | sed 's/Password hash: //g')
```

#### 設定網路區域

```yaml=
access_control:
  networks:
  - name: internal # 設定區域網路 Range
    networks:
    - 10.0.0.0/8
    - 172.16.0.0/12
    - 192.168.0.0/18
```

## 運用在SERVER

### 樹梅派安裝

官方有做 arm 版本。所以直接docker run 可以正常運行。我使用local版本，因為是在樹梅派關係，人數也沒有很多人在用，所以使用local版本。官方有lite,kubernetes 版本可以看。

### 程式參考設定

authelia/configuration.yml 設定

簡單記錄重點
1. 改多欄位key
2. 可針對區域網路做設定
3. rule 可設定多網域登入

```yaml=

---
jwt_secret: must_change_secret_key # 改變
default_redirection_url: https://public.xxxx.com.tw

server:
  host: 0.0.0.0
  port: 9091

log:
  level: debug

totp:
  issuer: xxxx.org.tw # authentication 登入名稱

authentication_backend:
  file:
    path: /config/users_database.yml

access_control:
  networks:
  - name: internal # 設定區域網路 Range
    networks:
    - 10.0.0.0/8
    - 172.16.0.0/12
    - 192.168.0.0/18

  default_policy: deny
  rules:
    - domain: xxxxxx.com.tw  # 設定網站規則
      policy: bypass
      networks:
      - internal  # 區域網路不做驗證
    - domain: xxxxxx.com.tw  # 設定網站規則
      policy: two_factor   # 雙重驗證
    - domain: secure.xxxx.com.tw
      policy: one_factor   # 密碼驗證

session:
  name: authelia_session
  secret: must_change_secret_key
  domain: xxxx.com.tw  # Should match whatever your root protected domain is

regulation: # 失敗次數會做 動作
  max_retries: 3
  # find_time: 120
  # ban_time: 300

storage:
  encryption_key: must_change_secret_key# 須設定key
  local:
    path: /config/db.sqlite3

notifier:
  filesystem:
    filename: /config/notification.txt # 沒有SMTP，可以到這邊看
...


authelia/users_database.yml
```yaml
users:
  user:
    displayname: "user"
    password: "取代 hash值"
    email: user@example.com
    groups:
      - admins
      - dev
```


### docker 設定


#### authelia
```
version: '3.3'

networks:
  traefik_network:
    external: true

services:
  authelia:
    image: authelia/authelia
    container_name: authelia
    volumes:
      - ./authelia:/config
    networks:
      - traefik_network
    labels:
      - 'traefik.enable=true'
      - 'traefik.http.routers.authelia.rule=Host(`authelia.xxxxx.xxxxx.org`)'
      - 'traefik.http.routers.authelia.tls=true'
      - 'traefik.http.routers.authelia.tls.options=default'
      - 'traefik.http.middlewares.authelia.forwardauth.address=http://authelia:9091/api/verify?rd=https://authelia.xxxxx.xxxxx.org'  
      - 'traefik.http.middlewares.authelia.forwardauth.trustForwardHeader=true'
      - 'traefik.http.middlewares.authelia.forwardauth.authResponseHeaders=Remote-User,Remote-Groups,Remote-Name,Remote-Email' 
    expose:
      - 9091
    restart: unless-stopped
    healthcheck:
      disable: true
    environment:
      - TZ=Asia/Taipei


```

### 服務設定label

主要重點只有一個`traefik.http.routers.ncp.middlewares=authelia@docker`，請參考下面設定。

```
version: "3"

services:
  nextcloudpi:
#    image:  ownyourbits/nextcloudpi
#    後來發現 nextcloudpi 有 nextcloudpi-armhf image
    image: ownyourbits/nextcloudpi-armhf
    command: "192.168.1.202"
    networks:
        - traefik_network
    labels:
      - "traefik.backend=ncp"
      - "traefik.http.routers.ncp.rule=Host(`xxx.xxx.xxx.org`)"
      - "traefik.http.routers.ncp.tls=true"
      - "traefik.http.services.ncp.loadbalancer.server.port=80"
      - 'traefik.http.routers.ncp.middlewares=authelia@docker'
    restart: always
    ports:
      #- "1180:80" # 暫時使用
      #- "11443:443" # 暫時使用
      - "4443:4443"
    volumes:
      - /media/USBdrive/ncp2:/data
      - /etc/localtime:/etc/localtime:ro
    environment:
      - "UMASK=002"

networks:
  traefik_network:
    external: true

```