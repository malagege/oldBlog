---
title: 快速搭建 web 反向代理 server
date: 2020-11-24 21:40:51
tags: [proxy]
categories: Linux
---

之前有聽過 apache, nginx, frp, caddy server 都能做 web 反向代理
但都沒有實際工作用到
最近因為有搭建 mockapi 和 jboss 想對外測試line webhook
有想到代理對外窗口使用 ngrok
但...反向代理花了不少時間
希望下次複製及所用
這邊應該會以window 環境
畢竟公司開發環境是 window

<!--more-->

之前有設定過 caddy server 1.0
反向代理非常好設定
但是從 caddy server 2.0 設定檔完全不會設定
然後 http => https 強制
相關都關不掉，找不到取消方法
只能乖乖用回其他工具

## nginx(方便好設定)

### nginx (window)

nginx 有 window 免安裝 zip

```bat
# 啟動 nginx
start nginx
# 關閉 nginx
 ./nginx.exe -s stop
```

### 反向代理設定



```conf
server{
    listen 80;
    # server_name 沒設定localhost 的話，只有 127.0.0.1 連的到
    server_name localhost;
    location /mockapi/ {
       
        # 後端的 Web Server, 即真實伺服器:
        proxy_pass http://127.0.0.1:8081/;
 
        # 定義 header 變數, 記錄使用者的 IP
        proxy_set_header X-Real-IP $remote_addr;
        
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 
        proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
        proxy_max_temp_file_size 0;
    }
    location /web/ {
       
        # 後端的 Web Server, 即真實伺服器:
        proxy_pass http://127.0.0.1:8080/;
 
        # 定義 header 變數, 記錄使用者的 IP
        proxy_set_header X-Real-IP $remote_addr;
        
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 
        proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
        proxy_max_temp_file_size 0;
    }
}
```

參考:[Nginx代理proxy pass配置去除前缀 - Ryan.Miao - 博客园](https://www.cnblogs.com/woshimrf/p/nginx-proxy-rewrite-url.html)

### 其他反向代理設定

location 要包在 server 裡面

/mockapi/ ==> 127.0.0.1:8081
/web/ ==> 127.0.0.1:8080

相對要連到
test.com/app/xxxxx =>  http://192.168.154.102/maped_dir/xxxxx

```
location /app/ {
    proxy_pass      http://192.168.154.102/maped_dir/;
}

```

非常簡單

test.com/app/8081 => http://192.168.154.102:8081

```
location ~ ^/app/(.*)$ {
    proxy_pass       http://192.169.154.102:$1;
}
```


test.com/app/request/xxxxx => http://192.168.154.102:9999/some_dir/xxxxx

```
location ~ ^/app/(.*)$ {
    proxy_pass       http://192.169.154.102:9999/some_dir/$1;
}
```

/app/hit/some/request/?name=xxxxx

=> http://192.168.154.102:9999/hit_page.php?path=some/request/&name=xxxxx

```
location  /app/ {
    rewrite    ^/app/hit/(.*)$ /hit_page.php?path=$1 break;
    proxy_pass   http://192.168.154.102:9999/some_dir/;
}
```

參考[Nginx proxy_pass: examples for how does nginx proxy_pass map the request | Yet Another Summer Rain](https://www.liaohuqiu.net/posts/nginx-proxy-pass/)


## frp

之前有有紀錄 [frp 內網穿透使用筆記 | 程式狂想筆記](https://malagege.github.io/blog/2020/11/15/frp-%E5%85%A7%E7%B6%B2%E7%A9%BF%E9%80%8F%E4%BD%BF%E7%94%A8%E7%AD%86%E8%A8%98/)
custom_domains、vhost_http_port 都需要設定

custom_domains 就設定 IP 就可以了
參考:[七、利用frp 穿透到内网的http/https网站，实现对外开放 - 三度 - 博客园](https://www.cnblogs.com/sanduzxcvbnm/p/8509150.html)

### frps

```
[common]
bind_port = 7000
vhost_http_port = 80
```

### frpc

```ini frpc.ini
[common]
server_addr = 127.0.0.1
server_port = 7000


[web01]
type = http
local_port = 8080
custom_domains = 127.0.0.1
locations = /web

[web02]
type = http
local_port = 8081
custom_domains = 127.0.0.1
locations = /mockapi
```

## 其他

caddy server
traefik 在想看看有空研究