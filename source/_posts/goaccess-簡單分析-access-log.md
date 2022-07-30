---
title: goaccess 簡單分析 access.log
date: 2022-07-30 23:25:02
tags: [log,gocaccess]
categories: 分析Log
---

這邊用 goaccess 分析 Jboss Log。

<!--more-->

```
goaccess access_log.log -o report.html --log-format=COMMON
```
https://raw.githubusercontent.com/allinurl/goaccess/master/config/goaccess.conf