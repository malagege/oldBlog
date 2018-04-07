---
layout: post
title: '重啟Linux桌面方法'
date: 2014-06-11 20:32
comments: true
categories: Linux
tags: Linux,tty,
---
最近使用Ezgo  11有時都會當機不動...
最後都跳到tty1直接`sudo reboot`

後來在想有沒有重啟桌面不重新開機方法...
在經過一直Google下最終找到了方法 :D

```
ps aux |grep tty 7
sudo kill {tty7 id}
```

原來就**殺掉tty**就會自動重啟!!!!
