---
title: 新手快速 Prometheus 使用 Docker 快速 10 分鐘建置環境
date: 2021-04-05 18:20:48
tags: [prometheus, exporter]
categories: Prometheus
---


首先，最近再找能跑的 prometheus 的 docker-compose 檔案
快速建置流程，體驗裡面介面功能

<!--more-->

## 快速實例

https://github.com/Kev1nChan/docker-prometheus


我找到一個 Github 實例，可以馬上 docker-compose 吃到 Node Exporter 設備
其他 Project 都不能順利執行...

可以先把grafana 版號拿掉，裝最新版

```yaml=
    image: grafana/grafana
```
git clone 下來
直接執行 `docker-compose up -d`



這邊可以window 可以把 `$PWD` 改成`.`
powershell 才不會執行有問題



> 使用前请修改Grafana配置文件中密码，当前admin密码为qwe123!@#


### 設定 Grafana

新增 DataSource ，選擇 Prometheus

![](https://i.imgur.com/RsmdEbZ.png)


注意!!這邊由於是裝在 docker 裡面
我們這邊 URL 路徑是要輸入 prometheus:


### 匯入 Dashboard 

很多教學，都沒有說要怎麼匯入別人寫好的 Dashboard 
剛用這個新手應該都不會用吧

https://grafana.com/grafana/dashboards/8919
這個網站可以找人加分享好用的 Dashboard

![](https://i.imgur.com/2UUIdQ3.png)


這邊 import 進去來後，記得要選 datasource


![](https://i.imgur.com/3GpZ9EU.png)


這邊 Grafana 就設定完了

### 通知教學



## 信箱設定

假設不想用真實信箱，可以用我之前fake stmp email 工具來設定
[maildev試用筆記 | 程式狂想筆記](https://malagege.github.io/blog/2018/12/09/maildev%E8%A9%A6%E7%94%A8%E7%AD%86%E8%A8%98/)

安裝maildev

```
docker run -d -p 1080:80 -p 1025:25 djfarrelly/maildev
```

```bash=
vim alertmanager/config.yml
```
設定 stmp 1025, tls = false ，帳號、密碼可以不用理(畢竟是假的...)

```yaml=

global:
  #163服务器
  smtp_smarthost: '192.168.1.113:1025'
  #发邮件的邮箱
  smtp_from: 'your-email@foxmail.com'
  #发邮件的邮箱用户名，也就是你的邮箱　　　　　
  smtp_auth_username: 'your-email@foxmail.com'
  #发邮件的邮箱密码
  smtp_auth_password: 'your-password'
  #进行tls验证
  smtp_require_tls: false

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 10m
  receiver: live-monitoring

receivers:
- name: 'live-monitoring'
  #收邮件的邮箱
  email_configs:
  - to: 'your-email@163.com'
```

讓你的 alertmanager 發送 mail
只需要讓你 node_exporter 結束程式就可以了

```bash=
docker-compose stop node_exporter   
```

執行完，稍後兩分鐘...(因為設定是兩分鐘)，可以看到mail 會發送到maildev

![](https://i.imgur.com/RewTNHy.png)

話說 granafan 也有通知功能

### discord 通知

[GitHub - benjojo/alertmanager-discord: Take your alertmanager alerts, into discord](https://github.com/benjojo/alertmanager-discord)

相關建置我後面文章有講到

### line 通知

[[教學] 如何使用 Line Notify 對群組進行通知 | 辛比誌](https://xenby.com/b/274-%E6%95%99%E5%AD%B8-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-line-notify-%E5%B0%8D%E7%BE%A4%E7%B5%84%E9%80%B2%E8%A1%8C%E9%80%9A%E7%9F%A5)
設定 line notify ，不需要服務登入
![](https://i.imgur.com/G110MnW.png)


https://stackoverflow.com/questions/53372935/how-can-i-config-prometheus-alert-with-line-notify
<--這個連結不能使用


**TOKEN記得要留著，否則之後不會找到**

記得要邀請 line notify 到聊天裡面去
![](https://i.imgur.com/Dyf1Fda.png)

否則不會收到

> 此帳號尚未被邀請至已連動「測試bot」的群組中。請將此帳號邀請至「去」中。

### 架設 line notify gateway

官方沒推薦使用哪一個line notify 
可能國外很少用

這篇有找到[line-notify-gateway (2). ใน blog… | by Nont Banditwong | Medium](https://nontster.medium.com/line-notify-gateway-2-f117c20c6f62)
雖然看不懂裡面文章
但從指令可以看出東西

```bash=
docker run -d -p 18081:18081 -e line-notify.personal-access-token='{換成你的TOKEN}' --name line-notify-gateway nontster/line-notify-gateway
```

```yaml= alertmanager/config.yml
receivers:
- name: line-noti
  webhook_configs:
  - url: http://192.168.73.161:18081/v1/alertmanager/payload
    send_resolved: true
```

測試alertmanager 訊息
```bash=
curl -H "Content-type: application/json" -X POST -d '{"recei"status":"firing","alerts":[{"status":"firing","labels":{"alertname":"InstanceDown","instance":"director1:9100","job":"ipvs","severity":"critical"},"annotations":{"description":"director1:9100 of job ipvs has been down for  more than 5 minutes.","summary":"Instance director1:9100 down"},"startsAt":"2019-06-09T09:12:56.795099989Z","endsAt":"0001-01-01T00:00:00Z","generatorURL":"http://72426f0cd030:9090/graph?g0.expr=up+%3D%3D+0&g0.tab=1"}],"groupLabels":{"alertname":"InstanceDown"},"commonLabels":{"alertname":"InstanceDown","instance":"director1:9100","job":"ipvs","severity":"critical"},"commonAnnotations":{"description":"director1:9100 of job ipvs has been down for  more than 5 minutes.","summary":"Instance director1:9100 down"},"externalURL":"http://alertmanager:9093","version":"4","groupKey":"{}:{alertname=\"InstanceDown\"}"}' http://localhost:18081/v1/alertmanager/payload
```

測試結果可進行運行

## 監控

裡面有內建設定 exporter 服務沒偵測到兩分鐘會發送通知
除了這些外，還有 traefik exporter ,blackbox exporter, 各種 exporter等等

[Exporters and integrations | Prometheus](https://prometheus.io/docs/instrumenting/exporters/)

接下來還有很長一段要學習的路