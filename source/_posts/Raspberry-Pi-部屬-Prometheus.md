---
title: Raspberry Pi 部屬 Prometheus
date: 2021-04-05 18:23:31
tags:
categories:
---

參考:[docker-prometheus/docker-compose.yml at master · Kev1nChan/docker-prometheus · GitHub](https://github.com/Kev1nChan/docker-prometheus/blob/master/docker-compose.yml)

<!--more-->

## 架設 Prometheus

調整部份 Raspberry PI

```yaml
version: '3.3'

volumes:
  prometheus_data: {}
  grafana_data: {}

networks:
  monitoring:
    driver: bridge

services:
  prometheus:
    image: prom/prometheus:v2.26.0
    container_name: prometheus
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - $PWD/prometheus/:/etc/prometheus/
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
    networks:
      - monitoring
    links:
      - alertmanager
      - cadvisor
    expose:
      - '9090'
    ports:
      - 9090:9090
    depends_on:
      - cadvisor

  alertmanager:
    image: prom/alertmanager:v0.21.0
    container_name: alertmanager
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - $PWD/alertmanager/:/etc/alertmanager/
    command:
      - '--config.file=/etc/alertmanager/config.yml'
      - '--storage.path=/alertmanager'
    networks:
      - monitoring
    expose:
      - '9093'
    ports:
      - 9093:9093

  cadvisor:
    image:  budry/cadvisor-arm:latest #gcr.io/cadvisor/cadvisor:v0.39.0
    container_name: cadvisor
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /:/rootfs:ro
      - /var/run:/var/run:rw
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
    networks:
      - monitoring
    expose:
      - '8080'

  node_exporter:
    image:  quay.io/prometheus/node-exporter:latest #prom/node-exporter:v0.18.0
    container_name: node-exporter
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - --collector.filesystem.ignored-mount-points
      - "^/(sys|proc|dev|host|etc|rootfs/var/lib/docker/containers|rootfs/var/lib/docker/overlay2|rootfs/run/docker/netns|rootfs/var/lib/docker/aufs)($$|/)"
    networks:
      - monitoring
    expose:
      - '9100'

  grafana:
    image: grafana/grafana:7.5.2
    user: "104"
    container_name: grafana
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - grafana_data:/var/lib/grafana
      - $PWD/grafana/provisioning/:/etc/grafana/provisioning/
    env_file:
      - $PWD/grafana/config.monitoring
    networks:
      - monitoring
    links:
      - prometheus
    ports:
      - 3000:3000
    depends_on:
      - prometheus

```


接下來設定在 Grafana 設定 Promethus Datasource 設定
就完成了

相關 alertmanager 調整請看 15分鐘建製 Promethus 環境

## Node Exporter

我原本以為 Node Exporter 要抓本機機器一定要用 Systemd 去執行程式
但看範例是用 Docker
感覺不一定要用 Systemd

這邊我機器都有安裝 Docker
所以我就沒選則用這個方式

使用 Systemd 方式記錄一下
可參考
1. [node_exporter 配置 - 简书](https://www.jianshu.com/p/7bec152d1a1f)
2. [Installing node_exporter as systemd serivice · GitHub](https://gist.github.com/jarek-przygodzki/735e15337a3502fea40beba27e193b04)

```
sudo tee /etc/systemd/system/node_exporter.service <<"EOF"
[Unit]
Description=Node Exporter

[Service]
User=node_exporter
Group=node_exporter
EnvironmentFile=-/etc/sysconfig/node_exporter
ExecStart=/usr/local/bin/node_exporter $OPTIONS

[Install]
WantedBy=multi-user.target
EOF
```


docker 要怎麼用呢?
其實上面就寫了

官方寫法跟我的有點不太一樣

```
docker run -d \
  --net="host" \
  --pid="host" \
  -v "/:/host:ro,rslave" \
  quay.io/prometheus/node-exporter:latest \
  --path.rootfs=/host
```

```yaml=
---
version: '3.8'

services:
  node_exporter:
    image: quay.io/prometheus/node-exporter:latest
    container_name: node_exporter
    command:
      - '--path.rootfs=/host'
    network_mode: host
    pid: host
    restart: unless-stopped
    volumes:
      - '/:/host:ro,rslave'
```

這邊不太明白 network_mode 要用 host 模式?

最我我選擇調整別台使用 docker

```yaml=

---
version: '3.8'

services:
  node_exporter:
    image:  quay.io/prometheus/node-exporter:latest 
    container_name: node-exporter
    restart: always
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - '--path.procfs=/host/proc'
      - '--path.sysfs=/host/sys'
      - --collector.filesystem.ignored-mount-points
      - "^/(sys|proc|dev|host|etc|rootfs/var/lib/docker/containers|rootfs/var/lib/docker/overlay2|rootfs/run/docker/netns|rootfs/var/lib/docker/aufs)($$|/)"
    expose:
      - '9100'
```


dockr-compose up 起來

prometheus/config.yml 修改
```yaml=
  - job_name: 'node-exporter'
    # Override the global default and scrape targets from this job every 5 seconds.
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    scrape_interval: 5s
    static_configs:
      - targets:
        - 'node_exporter:9100'
        - '192.168.1.xx:9100'
        - '192.168.1.xx:9100'
```

有遇到

```
ERROR: Version in "./docker-compose.yml" is unsupported. You might be seeing this error because you're using the wrong Compose file version. Either specify a supported version (e.g "2.2" or "3.3") and place your service definitions under the `services` key, or omit the `version` key and place your service definitions at the root of the file to use version 1.
For more on the Compose file format versions, see https://docs.docker.com/compose/compose-file/

```
請更新 docker-compose version

armv6 (pi 1 ) 不能跑
自己找網路上 build 都失敗
仔細想想，真的 PI 效能真的很低
所以就不放這個了


[1 Node Exporter for Prometheus Dashboard CN v20201010 dashboard for Grafana | Grafana Labs](https://grafana.com/grafana/dashboards/8919)
[Node Exporter Full dashboard for Grafana | Grafana Labs](https://grafana.com/grafana/dashboards/1860)

## tranmission 

- [GitHub - sandrotosi/simple-transmission-exporter: A simple Prometheus exporter for Transmission](https://github.com/sandrotosi/simple-transmission-exporter)
- [GitHub - metalmatze/transmission-exporter: Prometheus exporter for Transmission metrics, written in Go.](https://github.com/metalmatze/transmission-exporter)

仔細看了一下 dashboard 
兩個差異還滿大的
主要我是要看網路傳輸速度
所以我這邊選擇 python 版本

```bash=
git clone https://github.com/sandrotosi/simple-transmission-exporter.git

# 修改 Dockerfile 為 arm32v7
```

```dockerfile=
FROM arm32v7/python:3.9
...
```

```bash=
docker build -t transmission_exporter .

docker run -e TRANSMISSION_HOST=192.168.x.x \
           -e TRANSMISSION_PORT=9091 \
           -e TRANSMISSION_USERNAME=admin \
           -e TRANSMISSION_PASSWORD=xxx \
           -d -p 29091:29091 transmission_exporter
```

prometheus 設定增加後，重啟服務`docker-compose restart prometheus`
```yaml=
  - job_name: 'transmission-exporter' 
    # Override the global default and scrape targets from this job every 5 seconds.
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
      - targets:
        - '192.168.1.203:29091'

```

調整成 docker-compose 
換成 docker-compose 就不能運作了...
但一般docker 指令可正常執行...
真的很奇怪
```
version: '2'

services:
  transmission_exporter:
    build:
      context: .
      dockerfile: Dockerfile
    image: transmission_exporter
    container_name: transmission_exporter
    ports:
     - "29091:29091"
    environment:
      - "TRANSMISSION_HOST=192.168.1.203"
      - "TRANSMISSION_PORT=9091"
      - "TRANSMISSION_USERNAME=admin"
      - "TRANSMISSION_PASSWORD=admin"
```

## traefik 

```yaml=
    command:
      - "--api.insecure=true"
      ....(省略)
      - "--accesslog=true"
      - "--metrics.prometheus=true"

```

原生 traefik 就有支援 prometheus
加上這個`--metrics.prometheus=true`就能使用

granafan 加表[Traefik dashboard for Grafana | Grafana Labs](https://grafana.com/grafana/dashboards/4475)會看到
```
Panel plugin not found: grafana-piechart-panel
```

[docker-traefik-prometheus/config.monitoring at master · vegasbrianc/docker-traefik-prometheus · GitHub](https://github.com/vegasbrianc/docker-traefik-prometheus/blob/master/grafana/config.monitoring)
這邊有看到跑 docker 前
可以在這邊設定這個
這樣可以忽略下面安裝 plugin

```bash
[root@prometheus prometheus]# grafana-cli plugins install grafana-piechart-panel

installing grafana-piechart-panel @ 1.6.1

from: https://grafana.com/api/plugins/grafana-piechart-panel/versions/1.6.1/download

into: /var/lib/grafana/plugins

 

✔ Installed grafana-piechart-panel successfully 

 

Restart grafana after installing plugins . <service grafana-server restart>

[root@prometheus prometheus]# systemctl restart grafana-server.service 
```


參考:[解决Panel plugin not found: grafana-piechart-panel - 知我知行](https://lian.st/4077.html)


但我這邊是用docker
```
docker-compose exec grafana bash
grafana-cli plugins install grafana-piechart-panel
```

我後來是用這個 dashboard
[Traefik 2.2 dashboard for Grafana | Grafana Labs](https://grafana.com/grafana/dashboards/12250)




## process exporter

目前沒用到
是看程式有沒有執行
但目前相對程式都有 exporter
就沒有使用

## Blackbox Exporter

老實說，剛看好難入手
實作完才知道大概在做什麼

簡單來說，所有控制點都在 prometheus 
blackbox 都是收到接收做事情簡查

不多說，馬上實作
先`git clone https://github.com/prometheus/blackbox_exporter.git`下來

```bash
docker run --rm -d -p 9115:9115 --name blackbox_exporter -v `pwd`:/config prom/blackbox-exporter:master --config.file=/config/blackbox.yml
```

prometheus 設定
```yaml
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]  # Look for a HTTP 200 response.
    static_configs:
      - targets:
        - https://xxx.web   # Target to probe with https.
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 192.168.1.203:9115  # The blackbox exporter's real hostname:port.

  - job_name: blackbox-ping
    metrics_path: /probe
    params:
      module: [icmp]
    static_configs:
      - targets:
        - 192.168.1.xx   # <== Put here your targets
        - 192.168.1.xx   # <== Put here your targets
    relabel_configs:    # <== This comes from the blackbox exporter README
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 192.168.1.203:9115 # Blackbox exporter.

```

接下來重啟 `dokcer-compose restart prometheus`
就能解決了

參考:
[How to ping targets using blackbox_exporter with prometheus - Stack Overflow](https://stackoverflow.com/questions/55526487/how-to-ping-targets-using-blackbox-exporter-with-prometheus)



[Blackbox Exporter 小記 - Potioneer's Essays](https://william-yeh.net/post/2020/08/blackbox-exporter/)
[网络探测：Blackbox Exporter - prometheus-book](https://yunlzheng.gitbook.io/prometheus-book/part-ii-prometheus-jin-jie/exporter/commonly-eporter-usage/install_blackbox_exporter)

[grafana 7 监控https证书过期时间 - 海口-熟练工 - 博客园](https://www.cnblogs.com/fsckzy/p/14172262.html)


## 官方 exporter 清單
[Exporters and integrations | Prometheus](https://prometheus.io/docs/instrumenting/exporters/)


## 安裝 alertmanager-discord

首先 git clone 下來改 arm32v7 映像檔
golang build 參數調整
再 build 看看有沒有問題

```bash
git clone https://github.com/benjojo/alertmanager-discord.git
```

Dockerfile build 調整如下
中間有遇到一些狀況

```dockerfile
# Built following https://medium.com/@chemidy/create-the-smallest-and-secured-golang-docker-image-based-on-scratch                                                           -4752223b7324

# STEP 1 build executable binary
FROM arm32v7/golang as builder
# Install SSL ca certificates
RUN apt update
RUN apt install git
RUN apt install  ca-certificates
# Create appuser
RUN adduser appuser
COPY . $GOPATH/src/mypackage/myapp/
WORKDIR $GOPATH/src/mypackage/myapp/
#get dependancies
RUN go get -d -v
#build the binary
RUN CGO_ENABLED=0 GOOS=linux GOARCH=arm GOARM=7 go build -a -installsuffix cgo -ldflags="-w -s" -o /go/bin/alertma                                                           nager-discord


# STEP 2 build a small image
# start from scratch
FROM hypriot/rpi-alpine-scratch
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=builder /etc/passwd /etc/passwd
# Copy our static executable
COPY --from=builder /go/bin/alertmanager-discord /go/bin/alertmanager-discord

ENV LISTEN_ADDRESS=0.0.0.0:9094
EXPOSE 9094
USER appuser
ENTRYPOINT ["/go/bin/alertmanager-discord"]
```

```bash=
docker build -t alertmanager-discord .
```

完成，之前我寫的 line 測試的 curl 用在這個會失敗
直接設定到 alertmanager 測試正常

直接docker run 跑起來吧

```
docker run --rm -d -e DISCORD_WEBHOOK=https://discord.com/api/webhooks/xxxxxxx -p 9094:9094 alertmanager-discord
```

alertmanager/config.yml
```yaml
route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 10m
  receiver: discord_webhook

receivers:
- name: 'live-monitoring'
  #收邮件的邮箱
  email_configs:
  - to: 'your-email@163.com'
- name: 'discord_webhook'
  webhook_configs:
  - url: 'http://192.168.1.203:9094'

```


把一個 exporter 看會不會有任何通知

**這邊注意**
我用 discord-alertmanager 發現 job_name 不能設定 `_`樣子
導致有時候發送訊息會異常!!
這邊要注意一下


## 接下來學習目標

如何設定 alertmanger
發現 PromQL 還是有學必要
因為設定 alertmanger 必須要會這個東西
還有細項動態設定 label ...

先訂好一個大坑

* [Prometheus監控神器-Rules篇 - ⎝⎛CodingNote.cc ⎞⎠](https://codingnote.cc/zh-tw/p/170936/)
* [Alertmanager 紀錄 - 系統工程師 Beck Yeh 的技術分享](https://blog.beck-yeh.idv.tw/linux/monitor/prometheus-grafana/alertmanager/)
* [alertmanager/template at master · prometheus/alertmanager · GitHub](https://github.com/prometheus/alertmanager/tree/master/template)

https://www.itread01.com/content/1545670504.html

## 暫時用的 alertmanager rule

參考一些規則:[node_exporter 配置 - 簡書](https://www.jianshu.com/p/7bec152d1a1f)

```yaml
groups:
- name: example
  rules:

  # Alert for any instance that is unreachable for >2 minutes.
  - alert: service_down
    expr: up == 0
    for: 2m
    labels:
      severity: page
    annotations:
      summary: "Instance {{ $labels.instance }} down"
      description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 2 minutes."

  - alert: 磁盤容量小於 5%
    expr: 100 - ((node_filesystem_avail_bytes{job="node-exporter",mountpoint=~".*",fstype=~"ext4|xfs|ext2|ext3"} * 100) / node_filesystem_size_bytes {job="node-exporter",mountpoint=~".*",fstype=~"ext4|xfs|ext2|ext3"}) > 95
    for: 2m
    annotations:
      summary: "服務器實例 {{ $labels.instance }} 磁盤不足 告警通知"
      description: "{{ $labels.instance }}磁盤 {{ $labels.device }} 資源 已不足 5%, 當前值: {{ $value }}"

  - alert: "內存容量小於 20%"
    expr: ((node_memory_MemTotal_bytes - node_memory_MemFree_bytes - node_memory_Buffers_bytes - node_memory_Cached_bytes) / (node_memory_MemTotal_bytes )) * 100 > 80
    for: 2m
    labels:
      severity: warning
    annotations:
      summary: "服務器實例 {{ $labels.instance }} 內存不足 告警通知"
      description: "{{ $labels.instance }}內存資源已不足 20%,當前值: {{ $value }}"

  - alert: "CPU 平均負載大於 4 個"
    expr: node_load5 > 4
    for: 2m
    annotations:
      sumary: "服務器實例 {{ $labels.instance }} CPU 負載 告警通知"
      description: "{{ $labels.instance }}CPU 平均負載(5 分鐘) 已超過 4 ,當前值: {{ $value }}"

  - alert: "磁盤讀 I/O 超過 30MB/s"
    expr: irate(node_disk_read_bytes_total{device="sda"}[1m]) > 30000000
    for: 2m
    annotations:
      sumary: "服務器實例 {{ $labels.instance }} I/O 讀負載 告警通知"
      description: "{{ $labels.instance }}I/O 每分鐘讀已超過 30MB/s,當前值: {{ $value }}"

  - alert: "磁盤寫 I/O 超過 30MB/s"
    expr: irate(node_disk_written_bytes_total{device="sda"}[1m]) > 30000000
    for: 2m
    annotations:
      sumary: "服務器實例 {{ $labels.instance }} I/O 寫負載 告警通知"
      description: "{{ $labels.instance }}I/O 每分鐘寫已超過 30MB/s,當前值: {{ $value }}"

  - alert: "網卡流出速率大於 10MB/s"
    expr: (irate(node_network_transmit_bytes_total{device!~"lo"}[1m]) / 1000) > 1000000
    for: 2m
    annotations:
      sumary: "服務器實例 {{ $labels.instance }} 網卡流量負載 告警通知"
      description: "{{ $labels.instance }}網卡 {{ $labels.device }} 流量已經超過 10MB/s, 當前值: {{ $value }}"

  - alert: "CPU 使用率大於 90%"
    expr: 100 - ((avg by (instance,job,env)(irate(node_cpu_seconds_total{mode="idle"}[2m]))) *100) > 90
    for: 2m
    annotations:
      sumary: "服務器實例 {{ $labels.instance }} CPU 使用率 告警通知"
      description: "{{ $labels.instance }}CPU 使用率已超過 90%, 當前值: {{ $value }}"

  - alert: SSLCertExpiringSoon
    expr: probe_ssl_earliest_cert_expiry{job="blackbox"} - time() < 86400 * 30
    for: 1d
```

最後有看到這個網站有一堆 rule 可以參考
[Awesome Prometheus alerts | Collection of alerting rules](https://awesome-prometheus-alerts.grep.to/rules.html)