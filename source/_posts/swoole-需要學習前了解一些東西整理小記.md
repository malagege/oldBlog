---
title: swoole 需要學習前了解一些東西整理小記
date: 2019-09-20 22:44:54
tags: [swoole]]
categories: PHP
---

最近~~一時衝動~~報名 swoole 課程
我對高併發實作還滿感興趣
好奇怎麼運用一些方法解決
今天看了 [學習Swoole需要掌握哪些基礎知識-Swoole-Swoole文檔中心](https://wiki.swoole.com/wiki/page/487.html) 發現裡面很多名詞不會
不想浪費錢，趕快臨時抱佛腳

<!--more-->

本篇是我找來資訊，看到的內容打成自己的感想，所以也不是很正確的東西，反正是我自己筆記!!

## 多進程/多線程

多年來有看到執行緒跟多程序執行的差別，今天終於可以好好的想一下

### 程序 與 執行緒差別

其實很久以前有想這個問題，但一直沒有找解答
我有想出兩個差別，可以共用全域變數，另外一個不行
今天我又有更多想法，多程序吃的記憶體資源會比多執行緒吃的多
畢竟1個 electron 跟開 1000 個 electron 是有差的...
但我看到還有一個答案，就是多執行緒可以等待結束在進行下一個動作

詳細可看[Program/Process/Thread 差異 - Po-Ching Liu - Medium](https://medium.com/@totoroLiu/program-process-thread-%E5%B7%AE%E7%95%B0-4a360c7345e5)

### 


## SOCKET

> 瞭解SOCKET的基本操作如accept/connect、send/recv、close、listen、bind
> 瞭解SOCKET的接收緩存區、發送緩存區、阻塞/非阻塞、超時等概念

之前我有在 Java 書看到使用 socket 搭配執行緒做連線溝通
但沒有深入了解裡面原理

> 你一直聽到人家在講 "sockets"，你可能也想知道這些是什麼東西。
> 好的，其實它們就是：＂利用標準 UNIX file descriptors（檔案描述符）與其它程式溝通的一種方式＂。
[02-何謂 Socket - Beej's Guide to Network Programming 正體中文版](http://beej-zhtw.netdpi.net/02-what-is-socket)

### file descriptors(fd)

這邊有講到 `file descriptors` (fd) 後面會講到這個
簡單說 就是 0 標準輸出(stdout) 1 標準輸入(stdin) 2 標準錯誤(stderr)

一些重要的I/O導向:

> \>      重新導向並且先把檔案虧空(我真是敗家子:-))
> \>>     把結果導向並且append到檔案後
> <      重新導向standard input
> \>&n    把standard out丟給file descriptor n
> <&n    從file descriptor n拿東西給standard in
> \>&-    close standard out
> \<\<\text 把stanadard in導向，直到讀到text為止。


[File descriptor與I/O導向 @ 程式專欄 :: 隨意窩 Xuite日誌](https://blog.xuite.net/tzeng015/twblog/113272117-File+descriptor%E8%88%87I%2FO%E5%B0%8E%E5%90%91)

fd更狂的應用可以看[阿旺的 Linux 開竅手冊-file descriptor](http://www.polish.url.tw/ech2/ech2.html)
我都不知道`/proc/xxxx/fd`可以看到所有應用程式 stdin / stdout / stderr 檔案

```bash
# 取得cmd 的 PID
echo $$;
cd /proc/$$/fd/
ls -la
```
可以看到標準輸入輸出會寫在哪裡
當然裡還還有更多東西，目前先提到這樣

突然想到當初大學上 Linux 時候，老師講 Linux 裡面的東西都可以在檔案找到


[回憶の隨筆日記: 檔案描述符 (file descriptor) 簡單應用分析](http://inspire-future.blogspot.com/2016/12/file-descriptor.html)

[Socket-IO复用技术-SherryX-51CTO博客](https://blog.51cto.com/13097817/2054397)
[檔案描述符 - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/%E6%96%87%E4%BB%B6%E6%8F%8F%E8%BF%B0%E7%AC%A6)

### socket 跟 fd 關係? 

其實我以前看 socket 是傳訊息用的，想說他怎麼會跟 fd 有關係

#### bash 可以用 socket

```sh
# 開啟連線至 Google 網頁的 socket
exec 3<>/dev/tcp/www.google.com.tw/80

# 送出 HTTP 請求
echo -e "GET / HTTP/1.1\n\n" >&3

# 接收網頁內容，1 秒後自動停止接收資料
timeout 1 cat <&3

# 關閉輸入與輸出 socket
exec 3<&-
exec 3>&-
```

這時候可以去看 `/dev/xxx/fd/3` 可以看到這個檔案
不過我不確定別的程式語言建立 socket 會不會產生這個檔案??

我使用 `php -S 0.0.0.0:80` 建立簡單的 Server
使用`ps aux | grep php` 看 PID
進入 `/proc/{PID}/fd/` 使用 `ls -l` 可以看到一些檔案參照 socket
但是怎麼知道是輸入還是輸出，我就沒深入研究
所以我研判是 socket 跟 fd 是有關係的(應該 = =a)


[Bash 程式設計教學：開啟 TCP/UDP Socket 網路連線 - G. T. Wang](https://blog.gtwang.org/programming/bash-tutorial-open-tcp-udp-socket/)
[Bash shell 連接 Socket 寫法 | Tsung's Blog](https://blog.longwin.com.tw/2012/09/bash-shell-socket-connect-2012/)

我發現 zsh 不能實作，要在 bash 裡面一條一條執行才可以
用成 `xxx.sh` 不能跑，目前沒有深入找這個問題

這讓我想以前 TCP 概論課程有用 `telnet` 實作 接收 yahoo 網頁
這上面範例應該是 Linux 方法!!

### accept/connect、send/recv、close、listen、bind

{% asset_img socket.png socket %}

簡單小記一些 function 作用
`socket()` 建立 socket 用的 fd 
`bind()` socket() 傳回來的 fd 放在 bind 命名
`listen()` 監聽 socket，但重頭戲在 accept，需要下 accept() 別人才能練近來
`accept()` 接受別人連線，等 client 發 `connect`
`connect()` client 發起連線
`send() , recv()` write read TCP??
`sendto() , recvfrom()` write read UDP
`close` 關閉連線

首先，Server 會接收到 Client 資料，所以一開始 Server 會先做 **read**，確定收到資料才會做 write
簡單來講把這個想成 http 就好理解。話說 http 也是 socket o.<

想到這個我想想 socket 有沒有跟 https 憑證加密?
~~不過網路上查了一下，沒有看到。都是額外用 function library 加密~~
~~但也不是不能理解，畢竟 socket 介於 應用層 和 傳輸層中間~~
當然有找到使用 openssl ，不過沒有看到別的程式語言用 [socket编程之openssl入门 - 我们的征途是星辰大海 - CSDN博客](https://blog.csdn.net/qq_21111579/article/details/52134507)
[Java实现 SSL双向认证 Socket – Huang Shiyang](https://wp.huangshiyang.com/java%E5%AE%9E%E7%8E%B0-ssl%E5%8F%8C%E5%90%91%E8%AE%A4%E8%AF%81-socket)
[TLS Socket , client & server in NodeJS - Morioh](https://morioh.com/p/4ee89beb93bd/tls-socket-client-server-in-nodejs)
[1.7.4 SSL隧道加密TCP-Server-Swoole-Swoole文档中心](https://wiki.swoole.com/wiki/page/240.html)
看來還是真的有的...

題外話:我好奇 socket 是網路7層哪一層，裡面有寫[Socket 的哩哩扣扣 « Mark Lin Blog](http://marklin-blog.logdown.com/posts/2366799)

更多資訊
[TCP Socket Programming 學習筆記 | 雷德麥的藏書閣](http://zake7749.github.io/2015/03/17/SocketProgramming/)
[Beej's Guide to Network Programming 正體中文版 · GitBook (Legacy)](https://legacy.gitbook.com/book/myliao/beej-s-guide-to-network-programming-chinese-zhtw/details)

其實在看 io 複用一直在想為什麼要了解 socket
原來是大有關係，目前我猜測應該是 socket 並非最佳連線，所以才有 io 複用那些解決方案
到下面幾篇有很多都不是很了解，我盡量吸收

### 接收緩存區、發送緩存區、阻塞/非阻塞、超時等概念

[UDP Socket(套接字)——发送接收缓冲区 - 简书](https://www.jianshu.com/p/d3b6db70ad74)



## IO复用

[聊聊IO多路复用之select、poll、epoll详解 - 簡書](https://www.jianshu.com/p/dfd940e7fca2)

說真的我以為講到 IO 應該就會講到硬碟...
這一塊我不是很了解

### 同步、異步、阻塞、非阻塞

[聊聊同步、异步、阻塞与非阻塞 - 簡書](https://www.jianshu.com/p/aed6067eeac9)

看裡面的內容，會覺得同步和阻塞不會分辨
我就突然想到 js 的 ajax 是異步，那這個會是阻塞還是不是阻塞?
答案是異步非阻塞
[JS 同步、異步、阻塞、非阻塞 - Sam - Medium](https://medium.com/@mts40110/js-%E5%90%8C%E6%AD%A5-%E7%95%B0%E6%AD%A5-%E9%98%BB%E5%A1%9E-%E9%9D%9E%E9%98%BB%E5%A1%9E-29e1e1c0193e)
可以看上面連結
寫的還滿清楚的

### LINUX IO

[聊聊Linux 五种IO模型 - 簡書](https://www.jianshu.com/p/486b0965c296)







## 應用

[Swoole的简单运用 - 知乎](https://zhuanlan.zhihu.com/p/34279200)