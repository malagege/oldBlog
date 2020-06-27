---
title: Linux 限制網路速度方法
date: 2020-04-05 00:00:46
tags: [linux, 限速]
categories: Linux
---

最近發現動物機上傳速度高的話
容易造成別台機器網頁不能看(timeout)
原本想用中華電信小烏龜設定Qos
但好像沒有效果
網路上有看到 Linux 控制連線速度

<!--more-->

## wondershaper

```bash
# 以下不能做，不要執行
sudo apt install wondershaper

# 使用 apt 安裝執行會出現
# Please read the man page for the wondershaper and
# the file /usr/share/doc/wondershaper/README.Debian.gz

```

> ...but as of Ubuntu 18.04LTS, the packaged wondershaper does not work correctly out of the box so you have to go direct to the github repo from magnific0

好像...，apt 裝完不能用

```bash
wget https://github.com/magnific0/wondershaper/archive/master.zip -c -O wondershaper-tmp.zip && unzip -o wondershaper-tmp.zip && rm wondershaper-tmp.zip && cd wondershaper-master && sudo make install ; cd .. && rm -rf wondershaper-master
```

其實這些指令是抓檔案下來、解壓縮、刪除壓縮檔進去目錄，安裝程式完便刪除。

參考:[Linux 如何使用wondershaper來限制網路頻寬？ | MagicLen](https://magiclen.org/linux-bandwidth-limit/)

### 如何移除

因為使用 make install 安裝
所以不知道怎麼移除
到 wondershaper-master 做 `sudo make uninstall`

[If I build a package from source how can I uninstall or remove completely? - Ask Ubuntu](https://askubuntu.com/questions/87111/if-i-build-a-package-from-source-how-can-i-uninstall-or-remove-completely)

```
pi@raspberrypi:~/wondershaper-master $ sudo make uninstall
rm -f /usr/sbin/wondershaper;
rm -f /usr/lib/systemd/system/wondershaper.service;
rm -f /etc/conf.d/wondershaper.conf;
```

### 使用方法

```
MODES:
   wondershaper -a <adapter> -d <rate> -u <rate>
   wondershaper -c -a <adapter>
   wondershaper -s -a <adapter>

EXAMPLES:
   wondershaper -a eth0 -d 1024 -u 512
   wondershaper -a eth0 -u 512
   wondershaper -c -a eth0
```

記得輸入過的 wondershaper 的 interface 要重設速度
要做 `wondershaper -c -a <interface>`

## 算傳輸速度

[Convert Kbps to megabyte/second - Conversion of Measurement Units](https://www.convertunits.com/from/Kbps/to/megabyte/second)

EX 限制 2mb 換算 Kbps 16000
公式:大概 1mb = 8000kbps
但我應該是記不住，靠電腦算就好了XD



### 設定服務

重開機就會清除之前設定
他其實你打的指令也不會記錄
主要會吃/etc/systemd/wondershaper.conf
所以要改在這裏面

```bash
# 啟用 service
sudo systemctl enable wondershaper.service
# 停用 service
sudo systemctl disable wondershaper.service
```

奇怪，那我要設定多個 interface 要怎麼辦
我後來查都查不到
看到[Traffic management with WonderShaper [Documentation - Online.net]](https://documentation.online.net/en/dedicated-server/tutorials/network/configure-wondershaper)

/usr/lib/systemd/system/wondershaper.service

```
[Unit]
Description=Bandwidth shaper/Network rate limiter
After=network.target
Wants=network.target

[Service]
Type=oneshot
RemainAfterExit=yes
EnvironmentFile=/etc/systemd/wondershaper.conf
ExecStart=/usr/bin/wondershaper -a $IFACE -d $DSPEED -u $USPEED
ExecStop=/usr/bin/wondershaper -c -a $IFACE

[Install]
WantedBy=multi-user.target
```

其實看 ExecStart ，只會幫你帶入一個
感覺能透過 EnvironmentFile 加參數
透過 ExecStart 和 ExecStop 實現多個 interface 操作


[linux服務器使用WonderShaper進行網絡速度限制 – WONGCW 網誌](https://blog.wongcw.com/2019/05/07/linux%E6%9C%8D%E5%8B%99%E5%99%A8%E4%BD%BF%E7%94%A8wondershaper%E9%80%B2%E8%A1%8C%E7%B6%B2%E7%B5%A1%E9%80%9F%E5%BA%A6%E9%99%90%E5%88%B6/)

## Trickle

Trickle 我就不研究了


[【系統】Ubuntu : 限制網路頻寬 @ Ching Wei : This is My Life - 工作、攝影、旅遊 :: 痞客邦 ::](https://ching119.pixnet.net/blog/post/59675947)
[Linux下使用Trickle限制下載上傳帶寬 - 每日頭條](https://kknews.cc/zh-tw/code/3494a8y.html)
[Linux 如何使用wondershaper來限制網路頻寬？ | MagicLen](https://magiclen.org/linux-bandwidth-limit/)


## iptable + tc

感覺 wondershaper 是用 tc
但我看不懂 tc 設定

> wondershaper 是一個易用的流量限制腳本. 該腳本提供了以下改進:
>    降低交互流量時的延遲時間
>    允許上傳/下載的同時保留適當的 Web 瀏覽速度
>    確保上傳不會影響到下載
>    確保下載不會影響到上傳

[Wondershaper流量監控工具 - Ubuntu中文](https://wiki.ubuntu.org.cn/Wondershaper%E6%B5%81%E9%87%8F%E7%9B%91%E6%8E%A7%E5%B7%A5%E5%85%B7)

[程式扎記: [Linux 文章收集] TC 入門 (traffic control)](http://puremonkey2010.blogspot.com/2015/01/linux-tc-traffic-control.html)
[使用iptables和tc对端口限速 - 行木辛 - 博客园](https://www.cnblogs.com/xingmuxin/p/10793272.html)
[关于 iptables 和 tc 的限速理解 - 线上猛如虎，线下怂如鼠（WhyNotBetter） - SegmentFault 思否](https://segmentfault.com/a/1190000000666869)
[[Linux] TC(traffic control)流量控制實作筆記(1)](https://shofan.blogspot.com/2017/08/linux-tctraffic-control.html)
[iptables 限速 - 哈希](https://www.haxi.cc/archives/iptables-%E9%99%90%E9%80%9F.html)


## 其他工具

爬到[linux的traffic control@小鸟技术笔记](https://www.lijiaocn.com/%E6%8A%80%E5%B7%A7/2017/08/10/linux-net-traffic-control.html#trickle)有很多工具

dummynet
> dummynet通過創建pipe接管指定的報文，對其進行整型。

[利用Dummynet模拟恶劣网络环境_运维_baidu_zhongce的博客-CSDN博客](https://blog.csdn.net/baidu_zhongce/article/details/46874583)
其他惡劣環境方法:
* [模拟恶劣网络环境常用的几种解决方案 - 狂师 - 博客园](https://www.cnblogs.com/jinjiangongzuoshi/p/5272787.html)
* [如何模拟弱网测试 - 尚码园](https://www.shangmayuan.com/a/0fd3247055c54298b214d92c.html)
* [Linux模拟复杂网络环境下的传输（netem和tc） - 哈希](http://www.haxi.cc/archives/Linux%E6%A8%A1%E6%8B%9F%E5%A4%8D%E6%9D%82%E7%BD%91%E7%BB%9C%E7%8E%AF%E5%A2%83%E4%B8%8B%E7%9A%84%E4%BC%A0%E8%BE%93-netem%E5%92%8Ctc.html)

之前有在想要怎麼創造很糟網路環境，剛好看到這篇

pyshaper

> pyshaper是一個帶有gui的限速工具。