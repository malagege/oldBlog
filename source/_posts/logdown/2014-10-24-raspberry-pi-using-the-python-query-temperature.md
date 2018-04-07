---
layout: post
title: '[Raspberry Pi]使用Python查詢溫度'
date: 2014-10-24 02:40
comments: true
categories: Raspberry PI
---
```python cpuTemp.py
f = open("/sys/class/thermal/thermal_zone0/temp","r")
for t in f:
    print "CPU temp:"+t[:2]+"."+t[2:5]
f.close()
```