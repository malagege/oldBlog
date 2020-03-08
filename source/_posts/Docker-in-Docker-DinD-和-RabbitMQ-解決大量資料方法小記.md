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


### php 使用 socket 方式連 docker

參考官網文章
* [PHP: stream_socket_client - Manual](https://www.php.net/manual/en/function.stream-socket-client.php)
* [Examples using the Docker Engine SDKs and Docker API | Docker Documentation](https://docs.docker.com/develop/sdk/examples/)

```bash
curl --unix-socket /var/run/docker.sock -H "Content-Type: application/json" \
  -d '{"Image": "alpine", "Cmd": ["echo", "hello world"]}' \
  -X POST http:/v1.24/containers/create
```

發現 curl 需要 7.4 以上才有 unix-socket 連結功能
但使用 php curl 沒有這個問題

可以看到以下參考
使用前請記得 `docker pull alpine`

```php
<?php
$file_p = stream_socket_client("unix:///var/run/docker.sock", $errno, $errstr, 30);
if (!$file_p) {
    echo "$errstr ($errno)<br />\n";
} else {
    fwrite($file_p, "POST /containers/create HTTP/1.1\nHost: v1.24\nContent-Type: application/json\nContent-Length: 51\nConnection: Close\n\n{\"Image\": \"alpine\", \"Cmd\": [\"echo\", \"hello world\"]}");
    while (!feof($file_p)) {
        echo fgets($file_p, 1024);
    }
    fclose($file_p);
}
?>
```

`Connection: Close` 需要加，否則程式不會退出!!
仔細想想這樣也滿有道理，需要切斷 socket 連線
Content-Length 需要注意`{"Image": "alpine", "Cmd": ["echo", "hello world"]}`這個長度
不對的話會有問題

最後，程式用 DinD 要如何傳值到程式裡面，這也是一個不錯思考問題
畢竟會遇到跳脫字元(\\)，可能使用外部資源(file,mysql,redis)會是比較安全方式

* [如何用Telnet POST資料](http://blog.kenyang.net/2011/12/12/telnet-post)

對外 socket 連結可以改成 tcp 開頭

誤打誤撞，發現這個好像不錯工具[就是要你懂Unix Socket 进行抓包解析 | plantegg](https://plantegg.github.io/2019/04/04/%E5%B0%B1%E6%98%AF%E8%A6%81%E4%BD%A0%E6%87%82%E6%8A%93%E5%8C%85--Unix-Socket%E6%8A%93%E5%8C%85/){% asset_link web2.png 備份圖 %}
[就是要你懂网络监控--ss用法大全 | plantegg](https://plantegg.github.io/2019/10/12/%E5%B0%B1%E6%98%AF%E8%A6%81%E4%BD%A0%E6%87%82%E7%BD%91%E7%BB%9C%E7%9B%91%E6%8E%A7--ss%E7%94%A8%E6%B3%95%E5%A4%A7%E5%85%A8/)
[就是要你懂抓包--WireShark之命令行版tshark | plantegg](https://plantegg.github.io/2019/06/21/%E5%B0%B1%E6%98%AF%E8%A6%81%E4%BD%A0%E6%87%82%E6%8A%93%E5%8C%85--WireShark%E4%B9%8B%E5%91%BD%E4%BB%A4%E8%A1%8C%E7%89%88tshark/)

## 衍生問題

### RabbitMQ 的 worker 要如何關小保持關掉中程式可以正常結束

這個問題，我沒有在網路上爬到。
不過我自己想這個問題，在設計 worker 這條路應該要做到 `已經做過` 和 `尚未執行成功` 中斷程式
下次執行也能應該也要正常執行才對


## RabbitMQ 連續大量錯誤

[如何優雅地在RabbitMQ實現失敗重試 - lala - Medium](https://medium.com/@lalayueh/%E5%A6%82%E4%BD%95%E5%84%AA%E9%9B%85%E5%9C%B0%E5%9C%A8rabbitmq%E5%AF%A6%E7%8F%BE%E5%A4%B1%E6%95%97%E9%87%8D%E8%A9%A6-c050efd72cdb)  {% asset_link web1.png 備份圖 %}

其他問題想到再說

## rabbitmq 持久化

有 Queue 持久化
有 Message 持久化

## rabbitmq 定義 Queue 不能修改

## autoack 與 max-

可能要確定 autoack = true 時候， server 馬上發送 message 給 worker ??

[前言 · 轻松搞定RabbitMQ · 看云](https://www.kancloud.cn/longxuan/rabbitmq-arron/107717)

[Auto-Scaling Services Using Instrumented Metrics - Docker Flow Monitor](https://monitor.dockerflow.com/auto-scaling/)


[Spring Boot RabbitMQ - priority queue](https://programming.vip/docs/spring-boot-rabbitmq-priority-queue.html)