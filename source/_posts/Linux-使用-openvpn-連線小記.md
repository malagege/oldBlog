---
title: Linux 使用 openvpn 連線小記
date: 2020-06-25 19:10:20
tags: [linux]
categories: Linux
---

最近要做測試國外網域的問題
[VPN Gate: 公共 VPN 中继服务器列表](https://www.vpngate.net/cn/)
發現 VPN Gate 網站滿多 vpn 資訊
想使用 Linux 做操作
找了網路上資源
真的不是很多

<!--more-->

這邊簡單做一下筆記

```bash

# 前面可以用 VM 開啟，我這邊是用 vagrant
# 安裝 openvpn
sudo apt install openvpn tmux

# 下載 openv，可從 vpn gate 那邊下載
wget https://..../...ovpn 這邊下載檔名不能跟下載不一樣，建議還是用瀏覽器下載

# 開啟 tmux ，因為連接需要多開，
tmux 

# 執行 openvpn
openvpn ./xxxx.ovpn

# 切回 command line
Ctrl+b  and d

ifconfig
# enp0s3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
#         inet 10.0.2.15  netmask 255.255.255.0  broadcast 10.0.2.255
#         inet6 fe80::e8:2fff:fe1c:32d4  prefixlen 64  scopeid 0x20<link>
#         ether 02:e8:2f:1c:32:d4  txqueuelen 1000  (Ethernet)
#         RX packets 75995  bytes 94960003 (94.9 MB)
#         RX errors 0  dropped 0  overruns 0  frame 0
#         TX packets 13566  bytes 1040385 (1.0 MB)
#         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
#         inet 127.0.0.1  netmask 255.0.0.0
#         inet6 ::1  prefixlen 128  scopeid 0x10<host>
#         loop  txqueuelen 1000  (Local Loopback)
#         RX packets 36  bytes 3512 (3.5 KB)
#         RX errors 0  dropped 0  overruns 0  frame 0
#         TX packets 36  bytes 3512 (3.5 KB)
#         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# tun0: flags=4305<UP,POINTOPOINT,RUNNING,NOARP,MULTICAST>  mtu 1500
#         inet 10.242.23.205  netmask 255.255.255.255  destination 10.242.23.206
#         inet6 fe80::aad3:fe27:fda5:76d3  prefixlen 64  scopeid 0x20<link>
#         unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 100  (UNSPEC)
#         RX packets 2  bytes 1352 (1.3 KB)
#         RX errors 0  dropped 0  overruns 0  frame 0
#         TX packets 3  bytes 128 (128.0 B)
#         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

會多一個 interface( tun0 ) 這個部分就是有連接到 vpn 

```bash
curl ifconfig.me

# 看國家(看流量)
curl ipinfo.io

```

完成

