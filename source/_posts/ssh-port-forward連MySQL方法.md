---
title: ssh port forward連MySQL方法(SSH Tunnel)
date: 2018-12-13 21:28:46
tags: [mysql, port forward]
categories: MySQL
---

早上看到[Backend 台灣 (Backend Tw)](https://www.facebook.com/groups/616369245163622/permalink/1498950796905458/)
```
傳統老人要連上production db的方法：
...
２　把自己電腦的public key放到這台nano machine上的authorized_keys，以便自己電腦不用每次ssh都要輸入密碼。
３　每次要連上mysql時，先建立ssh port forwarding
ssh -L 9000:yourDBhostname:3306 smallPotato@abc.com
４　然後，看你喜歡用phpMyAdmin也好，mysql bench也好，連線上localhost:9000，這樣子便能連線上production db
```
以上內容來自[Backend 台灣 (Backend Tw) Triton Ho](https://www.facebook.com/groups/616369245163622/permalink/1498950796905458/)

之前有用過ssh tunnel 代理網頁來看
但沒想到可以用在MySQL
可能以前沒有深入理解這怎麼傳的 (>////<)

<!--more-->

早上看FB看到`ssh port forwarding`
沒想到還有這種操作

長期用phpmyadmin真的很痛苦
但是用這個方法可以用workbench連到MySQL(內網)
想說有沒有快速的方法

## DBeaver
[DBeaver Community | Free Universal Database Tool](https://dbeaver.io/)

連線設定可以設定SSH Tunnel
這樣以後一鍵迅速連MySQL
![](https://dbeaver.com/wp-content/uploads/2018/09/redis-connection-ssh.png)

設定非常簡單簡單，也不需要打ssh指令


## SSH 相關資料

由於我家裡沒有兩個不同網段
所以我就沒有實驗
但基於理解還是仔細爬了一下網路文章
不太想花太多時間研究
簡單整理一下

* Local port forwarding
```
ssh -L <local_port>:<target_host>:<target_port> remote_user@remote_host
```
* Remote port forwarding (Reverse SSH)
```
ssh -R 5900:localhost:22 guest@remote-pc
```
* SOCKS Proxy Forwarding
```
ssh -C -D 8080 cwliu@123.123.123.123
```

[SSH Tunnel](http://blog.codylab.com/ssh-port-forwarding/)
{% asset_link web1.png 備份圖 %}

Local port forwarding 和 SOCKS Proxy Forwarding
感覺兩個都好像

有看到[上班族ssh tunnel求生手冊 [論壇 - Ubuntu 與工具程式及軟體推薦] | Ubuntu 正體中文站](https://www.ubuntu-tw.org/modules/newbb/viewtopic.php?viewmode=compact&topic_id=17538)
{% asset_link web2.png 備份圖 %}
```
範例1：
在公司，透過外面的電腦當proxy，連到外面。
ssh -NfD 8888 remote_ip
-N : 不執行任何命令
-f : 在背景執行
-D : 建socks5 的proxy
這時候,瀏覽器和msn在socks 5 proxy設定的欄位填上localhost:8888 。
連出去的連驗就是加密，並且是穿過remot_ip的連線。

範例2:
在公司，透過外面的電腦連BBS。
ssh -NfL 2323:bbs.gamer.com.tw:23 remote_ip
-L : 將local port 轉向
telnet localhost 2323 就可以連到巴哈，而且是加密的。
也可用PCManx連localhost 2323也是一樣的意思。

範例3:
有時候假日，想要連回公司加個班。
但是公司是NAT，所以沒辦法這樣作。
可以運用TCP雙向傳輸的特性來辦到這件事。
ssh -NfR 2222:localhost:22 remote_ip
-R : 將remote port轉向 

....

範例一和範例二差別在於 SOCKS Proxy 的建立。這是因為 HTTP Proxy 會貼上/處理/取下額外的資訊，不能直接轉送(Forwarding)。範例二就真的是照本宣科地轉送。(當然，兩者轉送之前都有加密。)
```

簡單來說`Local port forwarding`只能對外一個port轉出去，所以不能用在看網頁上面
但是`SOCKS Proxy Forwarding`就可以對印remote可以連到的主機做連線(而且是多個)
這個用在網頁上面很方便


當然還有查了一些:
除了目前連mysql感覺其他地方目前用不上
所以先留著筆記


* [SSH Forwarding 內部網路連線 – 繁華上海城 ＆ 邊緣小宅女 – Medium](https://medium.com/@achilles1452/ssh-forwarding-%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E9%80%A3%E7%B7%9A-b79c13e2cdc8?fbclid=IwAR0HpLQ4tZhi5gEOZ37Ii-lS68_1_6ysL3nam7Aimaig1LeOwx1ciljZ9iY)
{% asset_link web3.png 備份圖 %}
* [SSH Tunnel](http://blog.codylab.com/ssh-port-forwarding/)
* [Reverse SSH Tunnel實際運用，搭配auotssh永不斷線，putty建立反向tunnel :: 哇哇3C日誌](https://ez3c.tw/2043)
* [使用 autossh 建立反向 SSH 连接 · 不立不破](https://blog.windrunner.me/sa/reverse-ssh.html)
* [利用SSH Tunnel連線至內部網路 - LinXblog](https://gwokae.mewggle.com/wordpress/2010/08/%E5%88%A9%E7%94%A8ssh-tunnel%E9%80%A3%E7%B7%9A%E8%87%B3%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF/)
* [用 SSH Tunnel 來避免防火牆阻擋實作與反制 | Mr. 沙先生](https://shazi.info/%E7%94%A8-ssh-tunnel-%E4%BE%86%E9%81%BF%E5%85%8D%E9%98%B2%E7%81%AB%E7%89%86%E9%98%BB%E6%93%8B%E5%AF%A6%E4%BD%9C%E8%88%87%E5%8F%8D%E5%88%B6/)
* [玩转SSH端口转发 | Fundebug博客](https://blog.fundebug.com/2017/04/24/ssh-port-forwarding/)
* [87showmin的筆記本: ssh port forwarding using Putty](http://87showmin.blogspot.com/2012/06/ssh-port-forwarding-using-putty.html)
* [SSH Tunnel 通道打造加密 Proxy，透過外部 Linux 伺服器上網 - G. T. Wang](https://blog.gtwang.org/linux/ssh-tunnel-socks-proxy-forwarding-tutorial/)
* [在 bash 中設定 Proxy server - 蘋果小豬筆記](http://applezulab.netdpi.net/02-linux-xi-tong-cao-zuo/set-proxy-server-in-bash)
* [What is my IP address? — ifconfig.co](https://ifconfig.co/)
* [上班族ssh tunnel求生手冊 [論壇 - Ubuntu 與工具程式及軟體推薦] | Ubuntu 正體中文站](https://www.ubuntu-tw.org/modules/newbb/viewtopic.php?viewmode=compact&topic_id=17538)