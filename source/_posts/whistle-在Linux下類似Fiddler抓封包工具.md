---
title: whistle - 在Linux下類似Fiddler抓封包工具
date: 2018-10-13 16:13:38
tags: [whistle]
categories: 實用工具
---

最近工司需要測試APP API
明天要帶我的Linux筆電到公司
沒有類似Fiddler工具
今天爬到一個好用的工具
簡單筆記一下

<!--more-->

首先有找到在Linux 下開啟[Linux(Ubuntu)环境下使用Fiddler - 海鸟 - 博客园](https://www.cnblogs.com/jcli/p/4474332.html)
但是我不通時裝其他的東西

[httproxy - Fiddler 在 linux/OSx 下的替代品? - SegmentFault 思否](https://segmentfault.com/q/1010000000094520)

裡面提到這些
* [Linux(Ubuntu)环境下使用Fiddler - 海鸟 - 博客园](https://www.cnblogs.com/jcli/p/4474332.html)
* [httproxy - Fiddler 在 linux/OSx 下的替代品? - SegmentFault 思否](https://segmentfault.com/q/1010000000094520)
* [deemstone/Dproxy: Web调试代理,类似Fiddler的工具. 完全Nodejs编写,跨平台. A smarty proxy tool for web debugging, it's works like Fiddler.](https://github.com/deemstone/dproxy)
* [Charles Web Debugging Proxy • HTTP Monitor / HTTP Proxy / HTTPS & SSL Proxy / Reverse Proxy](https://www.charlesproxy.com/)
* [Wireshark · Download](https://www.wireshark.org/download.html)
* [Fiddler Alternatives for Linux - AlternativeTo.net](https://alternativeto.net/software/fiddler/?platform=linux)
* [goddyZhao/nproxy: A cli proxy tool specialized in file replacing](https://github.com/goddyZhao/nproxy)
* [avwo/whistle: HTTP, HTTPS, WebSocket debugging proxy](https://github.com/avwo/whistle)
* [AlloyTeam/Rythem: a fiddler-like project using Qt](https://github.com/AlloyTeam/Rythem)
* [【AlloyTeam Web前端工具系列2】开源跨平台的Web抓包分析工具 Rythem (2012-09-19更新) | AlloyTeam](http://www.alloyteam.com/2012/05/web-front-end-tool-rythem-1/)


最後我選擇，[avwo/whistle: HTTP, HTTPS, WebSocket debugging proxy](https://github.com/avwo/whistle)
我Linux有裝nodejs，所以安裝設定有中文文件[关于whistle · GitBook](http://wproxy.org/whistle/)

安裝方法
```
npm install -g whistle
```

啟動
```
w2 start
```

結束
```
w2 stop
```

就這麼簡單!!!!

{% asset_img 1.png "whistle" %}

在接下來設定Android Wifi Proxy就完成了


查看封包也是看Web介面xxx.xxx.xxx.xxx:8899

但是設定proxy也是同一個port
這是什麼巫術XDD
