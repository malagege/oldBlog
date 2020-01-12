---
title: Docker in Docker (DinD) 和 RabbitMQ 解決大量資料方法小記
date: 2020-01-12 01:26:59
tags: [docker, rabbitmq]
categories: Docker
---

最近做大量資料處理
我想使用 RabbitMQ 處理大量資料
不過這個方式 RabbitMQ 會遇到 Work 需要一開始常駐
不能做到自動調整控制 Work 數量
所已採用 DinD 觀念去實作

<!--more-->

DIND 就是 Docker in Docker
```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock docker:latest version
```

然後容器裡面的 Container 可以跟 docker.sock 做溝通
相關可以控制方法 (Docker )

* [Examples using the Docker Engine SDKs and Docker API | Docker Documentation](https://docs.docker.com/develop/sdk/examples/)
* [关于/var/run/docker.sock | Fundebug博客 - 一行代码搞定BUG监控 - 网站错误监控|JS错误监控|资源加载错误|网络请求错误|小程序错误监控|Java异常监控|监控报警|Source Map|用户行为|可视化重现](https://blog.fundebug.com/2017/04/17/about-docker-sock/)

更多 Unix-socket 方式可以參考
[Engine API v1.24 | Docker Documentation](https://docs.docker.com/engine/api/v1.24/)
[Docker Engine API v1.40 Reference](https://docs.docker.com/engine/api/v1.40/)

看到這個方式突然想到之前 COSCUP 有看到 Serverless 方式去做

* [Philipz Docker Lab. - Serverless 架構 & Docker](https://philipz.github.io/serverless.html)
* [Building serverless apps with Docker - Docker Blog](https://www.docker.com/blog/building-serverless-apps-with-docker/)
* [無伺服器架構 - 壹讀](https://read01.com/RRQMLQ.html)
* [bfirsh/serverless-docker: Build serverless apps with Docker](https://github.com/bfirsh/serverless-docker)
* [bfirsh/serverless-docker-voting-app: A serverless web app built with Docker](https://github.com/bfirsh/serverless-docker-voting-app)
* [技术|用 Docker 创建 serverless 应用](https://linux.cn/article-7525-1.html)

但由於要限制 docker 執行數量
可以使用 Redis 用程式邏輯限制數量
不過要如何實作，這篇不是重點

## 衍生問題

### RabbitMQ 的 worker 要如何關小保持關掉中程式可以正常結束

這個問題，我沒有在網路上爬到。
不過我自己想這個問題，在設計 worker 這條路應該要做到 `已經做過` 和 `尚未執行成功` 中斷程式
下次執行也能應該也要正常執行才對


其他問題想到再說


[Auto-Scaling Services Using Instrumented Metrics - Docker Flow Monitor](https://monitor.dockerflow.com/auto-scaling/)