---
title: Docker compose 做 scale 筆記
date: 2022-02-13 23:17:54
tags: [docker,scale]
categories: Docker
---

## 使用方法


舊版可以用`docker-compose scale ...`不過新版改成 `docker-compose up --scale web=2`，剛好最近學。順便紀錄一下問題。

<!--more-->

## 範例

這邊簡單建立 websocket 測試，nodejs是參考網路上[JavaScript | WebSocket 讓前後端沒有距離. WebSocket 是網路協定的一種， Client 可以透過此協定與… | by 神Q超人 | Enjoy life enjoy coding | Medium](https://medium.com/enjoy-life-enjoy-coding/javascript-websocket-%E8%AE%93%E5%89%8D%E5%BE%8C%E7%AB%AF%E6%B2%92%E6%9C%89%E8%B7%9D%E9%9B%A2-34536c333e1b)範例。

main.js
```javascript=
//import express 和 ws 套件
const express = require('express')
const SocketServer = require('ws').Server

//指定開啟的 port
const PORT = 3000

//創建 express 的物件，並綁定及監聽 3000 port ，且設定開啟後在 console 中提示
const server = express()
    .listen(PORT, () => console.log(`Listening on ${PORT}`))

//將 express 交給 SocketServer 開啟 WebSocket 的服務
const wss = new SocketServer({ server })

//當 WebSocket 從外部連結時執行
wss.on('connection', ws => {
    console.log('Client connected')

    //對 message 設定監聽，接收從 Client 發送的訊息
    ws.on('message', data => {
        //data 為 Client 發送的訊息，現在將訊息原封不動發送出去
        ws.send(`${process.env.HOSTNAME}: response` + data)
    })

    ws.on('close', () => {
        console.log('Close connected')
    })
})
```

dockerfile
```dockerfile=
FROM node
COPY main.js package.json .
EXPOSE 3000
RUN npm install
ENTRYPOINT npm start
```

docker-compose.yml
```yaml=
version: '3'
services:
  node-app:
    image: 'nodejswebsocket'
    ports:
      - 3000

  nginx:
    image: nginx:stable-alpine
    ports:
      - 8000:80
    depends_on:
      - app
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
      # - ./var/log/nginx:/var/log/nginx
```

nginx.conf
```
map $http_upgrade $connection_upgrade {
    default Upgrade;
    '' close;
}

upstream websocket {
    server 192.168.0.10:9999;
}

server {
  listen  80 default_server;
  location / {
    proxy_pass http://node-app:3000;
	proxy_http_version 1.1; #WebSocket
	proxy_set_header Upgrade $http_upgrade; #WebSocket
	proxy_set_header Connection $connection_upgrade; #WebSocket   
  }
}
```


![](https://i.imgur.com/psDOJDg.png)


預設 port 會隨機分配。

![](https://i.imgur.com/YHOvINY.png)


## 遇到錯誤問題

### docker-compose port 雷

這邊不能使用`3000:3000`，因為開多個container會撞到port

```yaml=
version: '3'
services:
  node-app:
    image: 'nodejswebsocket'
    ports:
      - 3000:3000
```

![](https://i.imgur.com/J7YNGgO.png)


### 需要架一個 SLB 分流


雖然內部node-app是會自動做分流，但對外連線沒辦法使用`node-app`來訪問，所以需要架設nginx 做反向代理，當然也能使用traefik 做。

