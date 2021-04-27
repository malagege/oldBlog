---
title: 抓出 Docker 裡面檔案設定檔出來
date: 2021-04-15 21:35:32
tags: [docker]
categories: Docker
---

之前這篇有提到 cp 指令[Docker 新手筆記 | 程式狂想筆記](https://malagege.github.io/blog/2017/08/20/logdown/2017-08-20-2201711/)
但一直找有什麼方便複製 image 檔案
今天看到相關網站方法
整理出來

<!--more-->

```bash=
docker create --name prom_empty prom/prometheus
# docker create ：創建一個新的容器但不啟動它
docker cp prom_empty:/etc/prometheus/prometheus.yml ./prometheus.yml
```
參考:[Monitor your Raspberry Pi cluster with Prometheus and Docker](https://blog.alexellis.io/prometheus-nodeexporter-rpi/)

