---
title: Raspberry Pi 使用 Docker 架設 flexget
date: 2020-01-08 22:52:53
tags: [pi, flexget, docker]
categories: Linux
---

之前 PI 1號機裝 [HypriotOS: Back Again and Better then Ever · Docker Pirates ARMed with explosive stuff](https://blog.hypriot.com/post/hypriotos-back-again-with-docker-on-arm/)
內建 Docker 不需要手動安裝
相關教學可以看

* [如何在 Raspberry Pi 上安裝 Docker CE | IT 技術家](http://blog.itist.tw/2017/06/how-to-install-docker-ce-with-raspbian-jessie.html)
* [在 Raspberry Pi 2 運行 Docker](https://openhome.cc/Gossip/CodeData/DockerLayman/DockerLayman2.html)

<!--more-->

## 相關 arm Image 選擇

相關尋找來源可以看[armhf's Profile - Docker Hub](https://hub.docker.com/u/armhf/)
後來注意到**DEPRECATED**
裡面有建議從 [arm32v7](https://hub.docker.com/u/arm32v7/) 和 [arm32v6](https://hub.docker.com/u/arm32v6/) 去找
發現 arm32v7 我的 PI 1 不能用， PI 1 要使用 arm32v6 


## 安裝 flexget

目前測試網路上的 [breneser/flexget-arm: docker container for running flexget](https://github.com/breneser/flexget-arm)
可以順利安裝

### 安裝 flexget 最新版方法

其實把 image 換成 Python3 image 重新 build 就可以了

```dockerfile
FROM arm32v6/python:3.8.1-alpine3.10
MAINTAINER bren

RUN apk add --update \
  tzdata \
  python \
  py-pip \
  ca-certificates \
  && pip install --no-cache-dir --disable-pip-version-check flexget transmissionrpc \
  && rm -rf /var/cache/apk/*

# Copy local files
COPY files/ /

# add default volumes
VOLUME /config /data
WORKDIR /config

# expose port for flexget webui
EXPOSE 3539 3539/tcp

# run init.sh to set uid, gid, permissions and to launch flexget
RUN chmod +x /scripts/init.sh
CMD ["/scripts/init.sh"]

```

相關版本可以從[arm32v6/python - Docker Hub](https://hub.docker.com/r/arm32v6/python/) Tag上去挑版本
[arm32v6/python Tags - Docker Hub](https://hub.docker.com/r/arm32v6/python/tags)

## alpine 開 bash 方法

在 docker 內建沒有 bash

在 dockerfile 裝
```
RUN apk add --update bash && rm -rf /var/cache/apk/*
```

參考https://github.com/smebberson/docker-alpine/issues/43#issuecomment-226970518


## /usr/share/zoneinfo/xxxx: No such file or directory

tzdata 安裝這個能解決 上敘述問題
參考 [時間 · ubuntu 學習筆記](https://kejyuntw.gitbooks.io/ubuntu-learning-notes/system/system-date.html)
用 alpine 應該會遇到這個問題


## 最後...

最後發現 新版沒有 Config Editor
所以默默裝回去
有看到 [Config Editor](https://github.com/Flexget/webui/projects/3)
看來好像還沒完成

順便留一下版本紀錄
Version info
Flexget: 2.21.35 
API: 1.5.0

## 其他沒相關的紀錄

最近查有沒有監聽檔案新增方法，有找到 inotify-tool 
目前尚未整理，先貼到這邊...預防沒寫

* [sof-object-listing/inotify at master · prashanthpai/sof-object-listing](https://github.com/prashanthpai/sof-object-listing/tree/master/inotify)
* [inotify-tools使用方法介绍-Linux运维日志](https://www.centos.bz/2012/06/inotify-tools-introduction/)
* [Ubuntu 若檔案這樣，就那樣的好物 inotify-tools » 記下來 - 專業偽筆記](https://noter.tw/26/ubuntu-%E8%8B%A5%E6%AA%94%E6%A1%88%E9%80%99%E6%A8%A3%EF%BC%8C%E5%B0%B1%E9%82%A3%E6%A8%A3%E7%9A%84%E5%A5%BD%E7%89%A9-inotify-tools/)
* [inotify-tools工具inotifywatch实时监控网站目录_chaihongjun.me|柴宏俊web技术笔记](https://chaihongjun.me/os/linux/286.html)
* [inotify-tools + php脚本实现Linux服务器文件监控并邮件提醒](https://article.itxueyuan.com/WA56x)


相關未整理彩蛋

* [arm32v6/python Tags - Docker Hub](https://hub.docker.com/r/arm32v6/python/tags)
* [docker-alpine redis how to get into bash · Issue #43 · smebberson/docker-alpine](https://github.com/smebberson/docker-alpine/issues/43)
* [ytt - YAML Templating Tool](https://get-ytt.io/#example:example-variable)
* [FlexGet](https://flexget.com/Web-UI)
* [Better Comments - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments)
* [30 Seconds of Knowledge](moz-extension://55de8dc9-68eb-48ea-93fa-9d8190675ab1/newtab.html)
* [从 PHP5.2 升级到 PHP7.1 遇到的函数参数问题 | Laravel China 社区](https://learnku.com/articles/9001/function-parameter-problems-encountered-by-upgrading-from-php52-to-php71)
* [Incredibly slow upload speeds · Issue #798 · s3fs-fuse/s3fs-fuse](https://github.com/s3fs-fuse/s3fs-fuse/issues/798)
* [amazon ec2 - S3fs performance improvements or alternative? - Server Fault](https://serverfault.com/questions/396100/s3fs-performance-improvements-or-alternative)
* [amazon s3 - How can I mount an S3 bucket to an EC2 instance and write to it with PHP? - Stack Overflow](https://stackoverflow.com/questions/16428552/how-can-i-mount-an-s3-bucket-to-an-ec2-instance-and-write-to-it-with-php)
* [使用 s3curl 访问 OpenStack Swift](https://lingxiankong.github.io/2017-07-28-s3curl-swift.html)
* [spring - Uploading to Amazon S3 via curl route - Stack Overflow](https://stackoverflow.com/questions/44751574/uploading-to-amazon-s3-via-curl-route)
* [deepsentry/aws-s3-curl: Upload a file to a S3 bucket](https://github.com/deepsentry/aws-s3-curl)
* [redis消息队列简单应用 - 掘金](https://juejin.im/post/5cd0178151882542616c4a23)
* [手码两万余字，SpringMVC 包教包会 - 江南一点雨 - 博客园](https://www.cnblogs.com/lenve/p/12100698.html)
* [使用curl和wget发送post请求_cws1214的专栏-CSDN博客](https://blog.csdn.net/cws1214/article/details/21440691)
* [年轻人的第一个OAuth2.0 Server-Hydra | 一只程序汪的自我修养 - 點部落](https://dotblogs.com.tw/liguobao/2018/12/30/132746)
* [開發者必備知識 - HTTP認證（HTTP Authentication） - Carson's Tech Note](https://carsonwah.github.io/http-authentication.html)
* [thephpleague/oauth2-server: A spec compliant, secure by default PHP OAuth 2.0 Server](https://github.com/thephpleague/oauth2-server)
* [schemalex/schemalex: Generate difference sql of two mysql schema](https://github.com/schemalex/schemalex)
* [Basic performance testing · Issue #22 · GoogleCloudPlatform/gcsfuse](https://github.com/GoogleCloudPlatform/gcsfuse/issues/22)
* [Laravel 和 Spring Boot 兩個 框架 比較創業篇（ 一： 開發效率） - 好文 - 码工具](http://www.matools.com/lang-cht/blog/190383692)
* [Portainer 透過 TLS 認證 - Yi Yang's Blog](https://yylin1.github.io/2019/01/28/portainer-set-tls/)
* [時間 · ubuntu 學習筆記](https://kejyuntw.gitbooks.io/ubuntu-learning-notes/system/system-date.html)
* [docker-alpine redis how to get into bash · Issue #43 · smebberson/docker-alpine](https://github.com/smebberson/docker-alpine/issues/43)