---
title: frp 內網穿透使用筆記
date: 2020-11-15 02:16:25
tags: 實用工具
categories: [frp, traefik, ssh tunnel]
---

最近看 frp 文章
我看反向代理不用設定在 server 那邊
這邊方法有點跟 traefik 跟 label 實作有點像
但網路上說這兩個用途有點不太一樣
我認真看一下 frp 官網文件
真的還是有差!!

<!--more-->

frp 目前我看沒法跟 traefik 抓 docker label 去做自動代理動作
frp, traefik 都可以做 tcp, http 反向代理(聽說前幾版 traefik 不支援 tcp 代理)

這邊來看，traefik 功能實用信應該大於 frp
但看一下細節功能還是不一樣

frp 可以做 stcp(類似ssh tunnel) 連線，這邊 traefik 目前沒有這個東西
xtcp, unix socket ...等等代理方法，這些 traefik 目前沒有這些

## frp 環境建置

這邊我就簡單用 Vagrant 建置幾個 VM 環境
[檢查大量不同 IP 跟 Port 方法 | 程式狂想筆記](https://malagege.github.io/blog/2020/03/07/%E6%AA%A2%E6%9F%A5%E5%A4%A7%E9%87%8F%E4%B8%8D%E5%90%8C-IP-%E8%B7%9F-Port-%E6%96%B9%E6%B3%95/)
這邊之前有寫過
這邊從這邊簡單複製過來改

```ruby
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Hello"
  
  config.vm.define "frp-server" do |web|
    web.vm.box = "ubuntu/xenial64"
    web.vm.network "private_network", ip: "192.168.50.2"
  end

  config.vm.define "frp-client1" do |db|
    db.vm.box = "ubuntu/xenial64"
    db.vm.network "private_network", ip: "192.168.50.4"
  end
# 其實我發現可以不用開很多 VM
# 可以用 tmux 多開 frpc ，假如要模擬測試多台連，可以用這個
#   config.vm.define "frp-client2" do |db|
#     db.vm.box = "ubuntu/xenial64"
#     db.vm.network "private_network", ip: "192.168.50.5"
#   end

#   config.vm.define "frp-client3" do |db|
#     db.vm.box = "ubuntu/xenial64"
#     db.vm.network "private_network", ip: "192.168.50.6"
#   end

#   config.vm.define "frp-client4" do |db|
#     db.vm.box = "ubuntu/xenial64"
#     db.vm.network "private_network", ip: "192.168.50.3"
#   end
end

```

vagrant up 會等機器開好


## frp 安裝教學

參考:[安装 frp 内网穿透工具 | 离线笔记 · OfflineNotes](https://canwdev.github.io/manual/setup-frp.html)

```sh
wget https://github.com/fatedier/frp/releases/download/v0.34.2/frp_0.34.2_linux_amd64.tar.gz
tar xzvf frp_0.34.2_linux_amd64.tar.gz
mv frp_0.34.2_linux_amd64 frp
```

這段看要不要在本機做或在 VM 做都可以
VM 記得要切到`/vagrant` 這邊做
這樣其他機器就不用再抓一次了


```ini
[common]
bind_port = 7000

dashboard_addr = 0.0.0.0 # 正式不要開
dashboard_port = 7500

# 仪表盘访问的用户名密码，如果不设置，则默认都是 admin
dashboard_user = admin
dashboard_pwd = admin

# auth token
token = abcd1234567890
```

```bash
# VM frp-server 可以使用 tmux 
vagrant ssh frp-server
cd /vagrant/frp
./frps -c frps.ini
```


http://192.168.50.2:7500 有看到畫面就是成功了


## port 轉發

### client 設定

安裝 apache2 簡單做

```sh
# 進去client1 
vagrant ssh frp-client1
# 安裝 apache2
sudo apt install apache2
curl localhost
# 看到web內容就是完成了
```

```
cp frpc.ini frpc1.ini
vim frpc1.ini
```

```ini

[common]
server_addr = 192.168.50.2
server_port = 7000
token = abcd1234567890

[web]
type = tcp
local_ip = 127.0.0.1
local_port = 80
remote_port = 8080
```

這邊沒用type= http ，因為 http 需要加`custom_domains = 192.168.50.2`樣子
http我好像會失敗，我先不用設定這麼麻煩
可以看一下[利用FRP穿透到内网的http/https网站，实现对外开放 - 知乎](https://zhuanlan.zhihu.com/p/150650291)
vhost_http_port也要設定
我就懶得測試了

## hfs

之前進去 frpc 就懶得寫了

```ini

[common]
server_addr = 192.168.50.2
server_port = 7000
token = abcd1234567890


[test_static_file]
type = tcp
remote_port = 8081
plugin = static_file
# 要对外暴露的文件目录
plugin_local_path = /tmp
# 用户访问 URL 中会被去除的前缀，保留的内容即为要访问的文件路径
plugin_strip_prefix = static
plugin_http_user = abc
plugin_http_passwd = abc
```

http://192.168.50.2:8081/static/
就可以進去了


其實仔細想想好像不需要太多 frp-client
frpc可以同一台 server可以開很多...

## 其他官網教學

- [为本地 HTTP 服务启用 HTTPS | frp](https://gofrp.org/docs/examples/https2http/)
- [安全地暴露内网服务 | frp](https://gofrp.org/docs/examples/stcp/)
- [点对点内网穿透 | frp](https://gofrp.org/docs/examples/xtcp/)

因為目前沒有要使用
本來想要寫 stcp
不過這個跟 ssh tunnel 差不多
之前有記錄這篇[ssh port forward連MySQL方法(SSH Tunnel) | 程式狂想筆記](https://malagege.github.io/blog/2018/12/13/ssh-port-forward%E9%80%A3MySQL%E6%96%B9%E6%B3%95/)
看上面教學還滿簡單了，這邊就不整理了

## 相關文章
- [MvsCode/frps-onekey: Frps 一键安装脚本&管理脚本 A tool to auto-compile & install frps on Linux](https://github.com/MvsCode/frps-onekey)
- [免费frp公共服务器列表是什么？花生壳轻松实现ftp服务-oray贝锐科技花生壳官网](https://hsk.oray.com/news/8583.html)
- [免费FRP内网穿透服务](https://freenat.ml/)
- ["远程办公" 整体解决方案-内网穿透 - 知乎](https://zhuanlan.zhihu.com/p/150740136)