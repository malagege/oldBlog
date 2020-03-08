---
title: docker swarm 一些指令相關整理
date: 2019-12-07 20:06:28
tags: [docker,swarm]
categories: Docker
---

## 基本教學網站

[twtrubiks/docker-swarm-tutorial: Docker Swarm 基本教學 - 從無到有 Docker-Swarm-Beginners-Guide📝](https://github.com/twtrubiks/docker-swarm-tutorial)

[Docker Swarm 管理节点高可用分析 — outmanzzq](https://outmanzzq.github.io/2019/06/18/docker-swarm-manager-ha/)

由於上面網站都教的非常清楚
我這邊只記錄如何操作跟小筆記(或我覺得重要的)
不會詳細介紹，我覺得以上都說得很細

<!--more-->

### managers

後續有相關指令 managers 才可以執行 (EX: docker service, docker node)
之後下的指令是要在 manager 用

### worker

manager 會分配 task 給 work 用

## 基本連線

要如何加入 manger 或 worker 呢?

其實只要看
`docker swarm join-token manager`
或
`docker swarm join-token worker`
就能看到要加入 manager / worker 指令

## docker node 

docker node ls

可以看到所有加入的 node


## docker service

docker service 簡單來說就像 docker run 的 swarm 版。

只能在 manager 執行，會自動分配在 node 執行
docker service create --name=my_nginx nginx
docker service create --detach=false --name my_nginx --mode replicated nginx


docker service update 
docker service update --publish-add 80 my_nginx

### scale

docker service scale my_nginx=5

### 兩種模式

replicated:簡單說就是自動分配
global   :簡單說就是全部 node 都裝

預設 replicated

[Docker Swarm概念与基本用法 · 零壹軒·笔记](https://note.qidong.name/2018/11/docker-swarm/)

### 關掉 service

docker dervice scale my_xxx=0

docker service rm  my_xxx

### docker 查看

docker service ps

### 查看日誌

docker service log

## docker stack

簡單來說， docker stack 就是 docker compose 的 swarm 版本。

docker stack deploy --compose-file=portainer-agent-stack.yml portainer

## portainer 

在使用 portainer 新增機器(endpoint)
會有 agent 跟 docker
選擇 docker ，由於 docker swarm 是叢集，所以部份建立 container 要去不同 endpint (node) 看東西

使用 agent 同一台 endpoint 就能看到全部

{% asset_img img1.png agent和docker %}

不過哪個比較好，我還沒有確定，我們公司是用 docker

設定 agent 沒有很順利，後來用完在此紀錄

### agent

```
$ curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml
$ docker stack deploy --compose-file=portainer-agent-stack.yml portainer
```
官網設定會有很大問題，後來發現只要是 portainer 用 stack 部屬就會有問題(卡在一開始 Load 登入頁面)
不過單獨使用 portainer container 就沒這個問題

```bash
#docker run -d -p 9000:9000 -p 8000:8000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer
# 可使用 docker swarm 注意 我把 volume 拿掉了
docker service create \
    --name portainer \
    --publish 9000:9000 \
    --publish 8000:8000 \
    --replicas=1 \
    --constraint 'node.role == manager' \
#    --mount type=bind,src=//path/on/host/data,dst=/data \    ##測試用，所以這個拿掉
    portainer/portainer

curl -L https://downloads.portainer.io/agent-stack.yml -o agent-stack.yml && docker stack deploy --compose-file=agent-stack.yml portainer-agent

```

在隨便加入一個到 endpoint 就可以了
至於官方的不知道為什麼不能用?
目前找不到方法...(我是在 play with docker 跑的)
也有可能跟環境問題

### agent 跟 docker 差異

{% asset_img img2.png agent與docker差異 %}

192.168.1.17 有兩個，有一個是 agent 跟 docker
你會發現 agent 跟別的 agent 數量是一樣的
照道理可能加一個 agent 就可以了


[Portainer一个轻量级的Docker环境管理UI_Kubernetes中文社区](https://www.kubernetes.org.cn/5883.html)
[Portainer 透過 TLS 認證 - Yi Yang's Blog](https://yylin1.github.io/2019/01/28/portainer-set-tls/)

## 使用 play with docker

做 manager 和 worker 有預設三個 template
我後來發現不是每一個 template 都有幫你做 swarm join
所以建議良好習慣使用 `docker node ls` 查看有哪些加入

## history

預設是 5 個

[linux - How to clear Docker task history - Stack Overflow](https://stackoverflow.com/questions/42364695/how-to-clear-docker-task-history)

docker swarm update --task-history-limit 5 


## 使用 swarm volume 可能問題

有時間研究再記錄

[Docker集群管理Swarm数据持久化 - Bigberg - 博客园](https://www.cnblogs.com/bigberg/p/8795265.html)
["三剑客”之Swarm应用数据持久化管理（volume 、bind 、 nfs）-DevOps(甘兵)-51CTO博客](https://blog.51cto.com/ganbing/2091292)


## swarm conjob

聽說 swarm 可以設定排程
我搜尋一下完全沒有看到
看到介紹有 schedule ，我以為內建應該會有排程東西，不過我猜這個跟 docker service 可能有關係
不過最後還是找到 [crazy-max/swarm-cronjob: 📅 Create jobs on a time-based schedule on Docker 🐳 Swarm](https://github.com/crazy-max/swarm-cronjob)
好像新增 label 就能設定排程

不過有看到類似看到每月最後一天執行程式
看到有人建議用 每月第一天
之前有寫過這篇解決[排程 crontab 時間設定月底執行方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/08/04/%E6%8E%92%E7%A8%8B-crontab-%E6%99%82%E9%96%93%E8%A8%AD%E5%AE%9A%E6%9C%88%E5%BA%95%E5%9F%B7%E8%A1%8C%E6%96%B9%E6%B3%95/)
由於我公司的主機用UTC時間，但設定跟程式排程不是UTC時間
所以必須使用這個方式完成

~~有空在補這個~~

**2019-12-09**

今天嘗試了一夏swarm-job
架設上非常方便
但有幾點要特別注意

### 安裝 swarm-cronjob

可以用 play wtih docker 測試，開 template 3 個 manager 2 個 worker
方法跟上面一樣
```bash
docker service create \
    --name portainer \
    --publish 9000:9000 \
    --publish 8000:8000 \
    --replicas=1 \
    --constraint 'node.role == manager' \
    portainer/portainer

curl -L https://downloads.portainer.io/agent-stack.yml -o agent-stack.yml && docker stack deploy --compose-file=agent-stack.yml portainer-agent

## 安裝 swarm-cronjob
docker service create --name swarm_cronjob \
  --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
  --env "LOG_LEVEL=info" \
  --env "LOG_JSON=false" \
  --constraint "node.role == manager" \
  crazymax/swarm-cronjob
```

其他更多方式看官方文件

### 執行注意

```
Set command to run the task command 需要下 command 
Set mode to replicated (default)  使用 replicated mode
Set replicas to 0 to avoid running task as soon as the service is deployed  一開始設定replicas=0，否則會議開始就會跑
Set restart_policy.condition to none. This is needed for a cronjob, otherwise the task will restart automatically restart_policy 請設定 none ，否則一開始會一直執行
```

```
docker service create --name=my_hello --replicas=0 --restart-condition=none --label  swarm.cronjob.enable="true" --label swarm.cronjob.schedule="*/2 * * * *" hello-world ./hello 
```


官方可以帶的 label，前面兩個是必帶
```
swarm.cronjob.enable : Set to true to enable the cronjob (required)
swarm.cronjob.schedule : CRON expression format to use. (required)
swarm.cronjob.skip-running : Do not start a job if the service is currently running (optional)
swarm.cronjob.replicas : Number of replicas to set on schedule in replicated mode. (optional, default 1)
```

有空再放 stack yml

### agent mode global

我發現我只要新增 node 加入 swarm，就會自動安裝 agent
portainer 什麼設定都不用做!!!!太猛啦


## 相關連結

加入最愛的連結，但我沒整理，我就放一些彩蛋吧XD

* [[Day 19] 在 Kubernetes 中實現負載平衡 - Ingress Controller - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10196261)
* [Docker Stack多服务编排](https://objcoding.com/2018/09/03/docker-stack/)
* [Docker Swarm 進階實戰 (1) - Super9](https://super9.space/archives/1653)
* [用 Docker Swarm 部署你的雲服務 (實作篇) - Soul & Shell Blog](https://blog.toright.com/posts/5598/%E7%94%A8-docker-swarm-%E9%83%A8%E7%BD%B2%E4%BD%A0%E7%9A%84%E9%9B%B2%E6%9C%8D%E5%8B%99-%E5%AF%A6%E4%BD%9C%E7%AF%87.html)
* [docker 快速學習自我挑戰 Day7 | Vincent's Corner](https://tingsyuanwang.github.io/blogs/2017/08/01/docker-%E5%BF%AB%E9%80%9F%E5%AD%B8%E7%BF%92%E8%87%AA%E6%88%91%E6%8C%91%E6%88%B0-Day7/)
* [[Docker 學習筆記] Run/Compose/Swarm Mode 幾個運行方式基本概念 – Tiger-Workshop Blog](https://blog.tiger-workshop.com/docker-notes-how-to-run-compose-swarm-mode/)
* [全网最通俗易懂的Kafka入门！ - Java3y - 博客园](https://www.cnblogs.com/Java3y/p/11982381.html)
* [Docker集群管理Swarm数据持久化 - Bigberg - 博客园](https://www.cnblogs.com/bigberg/p/8795265.html)
* [Docker Swarm叢集實踐——部署篇 - IT閱讀](https://www.itread01.com/content/1547272631.html)
* [Apache的三種工作模式（prefork/worker/Event）](https://www.hkposts.com/55989/apache%E7%9A%84%E4%B8%89%E7%A8%AE%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F%EF%BC%88prefork-worker-event%EF%BC%89/)
* [Running Cron Jobs container on 5-Node Docker Swarm Mode Cluster – Collabnix](https://collabnix.com/running-cron-jobs-container-on-5-node-docker-swarm-mode-cluster/)
* [docker叢集管理工具--Swarm以及Portainer安裝 - IT閱讀](https://www.itread01.com/content/1548180022.html)
* [How to set the last day of the month? · Issue #234 · robfig/cron](https://github.com/robfig/cron/issues/234)
* [Portainer一个轻量级的Docker环境管理UI_Kubernetes中文社区](https://www.kubernetes.org.cn/5883.html)
* [使用 Haproxy + Keepalived 构建基于 Docker 的高可用负载均衡服务（一） - CODING 博客](https://blog.coding.net/blog/Haproxy&Keepalived)
* [Haproxy+Keepalived高可用环境部署梳理（主主和主从模式）-眼眸刻着你的微笑-51CTO博客](https://blog.51cto.com/dengaosky/2129856)
* [containers - How can I remove shutdown Docker Service tasks after a rolling update? - Stack Overflow](https://stackoverflow.com/questions/41705134/how-can-i-remove-shutdown-docker-service-tasks-after-a-rolling-update)
* [Docker Swarm 实战 - rj-bai 's Blog](https://blog.rj-bai.com/post/150.html)