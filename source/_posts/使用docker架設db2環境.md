---
title: 使用docker架設db2環境
date: 2018-11-08 20:49:45
tags: [docker,db2]
categories: Docker
---

最近要實驗boolean在db2
會有什麼結果
所以找找有沒有docker簡單架設方法
可以在docker api玩

<!--more-->


```bash
docker run -it -p 50000:50000 -e DB2INST1_PASSWORD=db2inst1-pwd -e LICENSE=accept ibmcom/db2express-c:latest bash
```

```
$ su - db2inst1
$ db2start
$ db2sampl
$ db2 connect to sample
$ db2 "SELECT * FROM STAFF"
```

有畫面就是正常

因為要實驗一個東西
這篇紀錄到這邊

參考來源：
[通过Docker安装db2express-c — 董仁文的博客](https://dongrenwen.github.io/2018/05/07/docker-install-db2/#%E4%BB%8E%E7%BD%91%E7%BB%9C%E8%8E%B7%E5%8F%96db2express-c%E9%95%9C%E5%83%8F)（沒有加上db2start）
[ibmcom/db2express-c - Docker Hub](https://hub.docker.com/r/ibmcom/db2express-c/)
[IBM Knowledge Center - 驗證分割的資料庫伺服器安裝（Linux 及 UNIX）](https://www.ibm.com/support/knowledgecenter/zh-tw/SSEPGG_10.5.0/com.ibm.db2.luw.qb.server.doc/doc/t0008501.html)