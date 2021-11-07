---
title: log4j 關掉不想要看的 log
date: 2021-11-07 22:04:08
tags: [java,log4j]
categories: Java
---



最近案子 User 不想看到這一串log，我就查詢有沒有不要顯示方法，最後找到記錄一下。

<!--more-->

![](https://i.imgur.com/7Ft2GNZ.jpg)


log4j 關掉
```
log4j.logger.org.springframework.beans.factory.support.DefaultListableBeanFactory=ERROR

```