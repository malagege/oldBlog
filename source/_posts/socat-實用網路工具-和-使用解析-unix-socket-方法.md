---
title: socat 實用網路工具 和 使用解析 unix socket 方法
date: 2020-02-14 17:17:15
tags: [linux,socat]
categories: Linux
---

最近要看 unix socket 傳送資料而苦惱
剛好最近這個工具[就是要你懂Unix Socket 进行抓包解析 | plantegg](https://plantegg.github.io/2019/04/04/%E5%B0%B1%E6%98%AF%E8%A6%81%E4%BD%A0%E6%87%82%E6%8A%93%E5%8C%85--Unix-Socket%E6%8A%93%E5%8C%85/)
馬上實作看看!!!

<!--more-->

## 簡單介紹

**So**cket **cat** 是一個比 netcat(nc) 強大工具。 nc 我沒怎麼使用過，不過直接先跳學 socat 使用好像也不錯，感覺比 nc 簡單。


## 首先怎麼抓到 unix socket 

```bash
sudo mv /var/run/docker.sock /var/run/docker.sock.original
sudo socat TCP-LISTEN:8089,reuseaddr,fork UNIX-CONNECT:/var/run/docker.sock.original
sudo socat UNIX-LISTEN:/var/run/docker.sock,fork TCP-CONNECT:127.0.0.1:8089
```

**以下是我想的流程，未必正確**
上方實作 socket ，正常流程

1. socket server 會做 等待接收資料(stdin)
client端: clinet socket (stdin) ---> server socket (對 client 來說是 stdout,server 來說是 stdin)

2. Server接到完資料，經過程式處理，會回傳資料到client
Server端: server socket (stdin) ---> client socket (對 server 來說是 stdout,client 來說是 stdin)

所以正常應該會有來回資料

用上面方法，我發現 docker 怎麼沒有 docker 容器跑出來結果`docker run ubuntu echo hello`
經過一番測試

```
sudo mv /var/run/docker.sock /var/run/docker.sock.original
sudo socat -t100 -d -x -v UNIX-LISTEN:/var/run/docker.sock,mode=777,reuseaddr,fork UNIX-CONNECT:/var/run/docker.sock.original
```
用以上參數可以正常看到執行結果，又在想他有做轉發應該都會來回，應該可以做顯示
發現多了`-t100 -d -x -v`可以顯示結果
```
sudo mv /var/run/docker.sock /var/run/docker.sock.original
sudo socat -t100 -d -x -v TCP-LISTEN:8089,reuseaddr,fork UNIX-CONNECT:/var/run/docker.sock.original
sudo socat -t100 -d -x -v UNIX-LISTEN:/var/run/docker.sock,fork TCP-CONNECT:127.0.0.1:8089
```
這樣就正常了

`-t<timeout>`
沒想到是跟這個有關係，設定-t100 就能顯示
> When one channel has reached EOF, the write part of the other channel is shut down. Then, socat waits <timeout> [timeval] seconds before terminating.  Default  is  0.5  seconds.
> This  timeout only applies to addresses where write and read part can be closed independently. When during the timeout interval the read part gives EOF, socat terminates without   awaiting the timeout.

`-v` http 的話可服選擇這種方式去顯示記錄
`-x` 也是顯示封包格式

## 相關指令

### 讀寫文件

```
socat - /xxx/note.txt
socat - ./note.txt
```
我一直搞不懂 - 意思
不過 第二個 address 是 STDOUT 突然就好懂了
不知道`-`是不是呼略的意思?

通常 socat [ option ] address1 address2

address1->address2


### 開 port 通訊

```bash
# command a window
socat TCP-LISTEN:7000 -        
# command b window
socat - TCP:127.0.0.1:7000
```

### 

# 在服務端 7005 端口建立一個 Shell。

server
```
$ socat TCP-LISTEN:7005,fork,reuseaddr EXEC:/bin/bash,pty,stderr
或者
$ socat TCP-LISTEN:7005,fork,reuseaddr system:bash,pty,stderr
```

client

```
# 連接到服務器的 7005 端口，即可獲得一個 Shell。readline 是 GNU 的命令行編輯器，具有歷史功能。
$ socat readline tcp:127.0.0.1:7005
```
#### 我的ubuntu 不能用 readline 

> 問題: ubuntu下，按照官方文檔，客戶端執行socat readline tcp:serverip:12345，出現錯誤提示：socat[2067] E unknown device/address "READLINE"，本機上已經安裝readline相關庫。

### 文件傳送

服務端

socat -u open:filename,binary tcp-listen:12345

客戶端

socat -u tcp:serverip:12345 open:localfilename,create,binary



### 讀寫分離

socat支持打開端的讀寫分離，使用!!符號，左側表示讀，右側表示寫。

socat open:hello.html\!\!open:log.txt,create,append tcp-listen:12345,reuseaddr,fork

說明

open:hello.html 表示讀hello.html文件。
open:log.txt 表示收到的數據寫入log.txt文件。
reuseaddr 見socket的SO_REUSEADDR。
fork 請求到達時，fork一個進程進行處理。
在bash下，需要用\對!進行轉義。

參考:[socat - szllq2000 - 博客园](https://www.cnblogs.com/SZLLQ2000/p/8591405.html)

### 通過 OPENSSL 加密傳送

參考: [Socat 入門教程 - 運維之美](https://www.hi-linux.com/posts/61543.html)

```bash
# 為服務端的證書取一個基本的名字。
FILENAME=server
# 生成公鑰私鑰對。
openssl genrsa -out $FILENAME.key 1024
# 生成一個自簽名的證書，會提示你輸入國家代號、姓名等，或者按下回車鍵跳過輸入提示。
openssl req -new -key $FILENAME.key -x509 -days 3653 -out $FILENAME.crt
# 用剛生成的密鑰文件和證書文件來生成PEM文件。
cat $FILENAME.key $FILENAME.crt >$FILENAME.pem
```

client 
```bash
FILENAME=client
# 生成公鑰私鑰對。
openssl genrsa -out $FILENAME.key 1024
# 生成一個自簽名的證書，會提示你輸入國家代號、姓名等，或者按下回車鍵跳過輸入提示。
openssl req -new -key $FILENAME.key -x509 -days 3653 -out $FILENAME.crt
# 用剛生成的密鑰文件和證書文件來生成PEM文件。
cat $FILENAME.key $FILENAME.crt >$FILENAME.pem
```

### 憑證問題

>  如果出现异常 2016/12/15 17:49:50 socat[8259] E certificate is valid but its commonName does not match hostname
>  可使用 verify=0 关闭验证
>  或者添加 commonname

參考: [网络相关工具集](http://wener.me/notes/ops/network/tool/intro/)

## 如何監聽 port 抓 GET POST 封包

### Wireshark

[0. 協定分析 : Wireshark | 宅學習](https://sls.weco.net/CollectiveNote20/Wireshark)

http.request.method == "POST"
[wireshark - Sniff HTTP packets for GET and POST requests from an application - Stack Overflow](https://stackoverflow.com/questions/3410505/sniff-http-packets-for-get-and-post-requests-from-an-application)

### tcpdump

過濾 GET 請求:

sudo tcpdump host 3sd.me and port 80 and 'tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420'

過濾 POST 請求：

sudo tcpdump host 3sd.me and port 80 and 'tcp dst port 80 and (tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x504f5354)'

參考:
[使用 wireshark 查看 tcpdump 的抓包結果 - Huang Huang 的博客](https://mozillazg.com/2015/05/open-tcpdump-result-with-wireshark-gui.html)
[使用 tcpdump 抓取 POST 動作的 request 資料內容 | SSORC.tw](https://ssorc.tw/6553/%E4%BD%BF%E7%94%A8-tcpdump-%E6%8A%93%E5%8F%96-post-%E5%8B%95%E4%BD%9C%E7%9A%84%E5%85%A7%E5%AE%B9/)


## 相關教學網站

* [用ssh和socat分分钟搞定Linux各种端口转发 - 知乎](https://zhuanlan.zhihu.com/p/33306055)
* [TCPDump Capture HTTP GET/POST requests - Apache, Weblogic & Websphere](https://www.middlewareinventory.com/blog/tcpdump-capture-http-get-post-requests-apache-weblogic-websphere/)
* [SOCAT 简介 - BrieflyX's Base](http://brieflyx.me/2015/linux-tools/socat-introduction/)
* [Socat 入门教程 - 运维之美](https://www.hi-linux.com/posts/61543.html)
* [socat - Cindy Sridharan - Medium](https://medium.com/@copyconstruct/socat-29453e9fc8a6)
* [socat使用笔记 - young525 - 博客园](https://www.cnblogs.com/young525/p/5873727.html)
* [socat | 夢想家](https://datahunter.org/socat)
* [依云's Blog](https://blog.lilydjwg.me/tag/socat)
* [tcpdump 抓包使用小结 | HelloDog](https://wsgzao.github.io/post/tcpdump/)


### 查詢到工具

cat、netcat、rinetd、stunnel 

FRP
[推荐一款很好用的内网穿透工具--FRP](https://mp.weixin.qq.com/s/8HeeDC5x5xozElN8GzQLLw)

這篇算沒整理很好
很多網頁都整理不錯
大神們真的很厲害
我只能跪了