---
title: Loki 做監控動作方法
date: 2022-04-03 00:09:18
tags: [kubernetes,loki]
categories: Kubernetes
---

選 Loki 做監控主要是動物機架一個 Web 服務，天天查看 log 都有被攻擊跡象。我使用 Loki LogQL 去做查詢，雖然有做一層 網域才顯示內容，前正子使用 Autleia 做 雙因子驗證，更久以前還有設定 iptable 台灣 IP 才能連，但現在有做 TLS 關係，所以不能擋 IP，不知道 Docker 能不能跟 fail2ban 做結合。最近也有看 traefik plugnin 也有fail2ban ，改天有空也能研究看看。

這兩天還攻擊滿可觀的。
![](https://i.imgur.com/GNbpxmu.png)

查詢不友善的IP訪問，這邊我之前寫的 LogQL，暫時放在這邊。之前想特別紀錄，但久久就忘記規則怎麼用，但所幸參考範例改一改還是能試出來。
```
{container_name="rpi-traefik_traefik_1",host="PI202"} | json | ClientHost!~"192.168.+|排除IP" RequestPath != "/"
```


```
{container_name="rpi-traefik_traefik_1",host="PI202"} | json | ClientHost!~"192.168.+|排除IP" RequestPath != "/" DownstreamStatus != "404"  | line_format "{{.ClientHost}}"
```


本章節(筆記)還是重點還是放在，Open Beta 主機架設 Kubernetes 怎麼抓 Loki log資訊做監控。

<!--more-->

## 流程

{% mermaid flowchart LR %}
    Promtail ----> Loki
    Promtail -- metrics 增加 prometheus 資料 --> Prometheus
    Prometheus --> AlertManager
{% endmermaid %}

主要參照[使用 Loki 进行日志监控和报警-阳明的博客|Kubernetes|Istio|Prometheus|Python|Golang|云原生](https://www.qikqiak.com/post/use-loki-monitor-alert/)文章，操作中發現現有版本設定有改，但修正也能正常使用。

## 實作流程

參考文章
- [使用 Loki 进行日志监控和报警-阳明的博客|Kubernetes|Istio|Prometheus|Python|Golang|云原生](https://www.qikqiak.com/post/use-loki-monitor-alert/)
- [Kubernetes轻量级日志收集系统Loki-Stack - 吕振江 - 博客园](https://www.cnblogs.com/lvzhenjiang/p/15639174.html#32-%E4%BF%AE%E6%94%B9valuesyaml%E6%96%87%E4%BB%B6)
- [How to Setup Alerting With Loki - Ruan Bekker's Blog](https://blog.ruanbekker.com/blog/2020/11/06/how-to-setup-alerting-with-loki/)

## 設定 loki-stack.yml(helm values.yml)


主要重點在這段
```yaml=
promtail:
  enabled: true
  pipelineStages:
  - match:
      selector: '{app="nginx"}'
      stages:
      - regex:
          expression: '.*(?P<hits>GET /.*)'
      - metrics:
          nginx_hits: 
            type: Counter
            description: "Total nginx requests"
            source: hits
            config:
              action: inc

```

`pipelineStages`可以看相關看官方文件，他這個有點像pipeline 交給後續處理，前面處理失敗，所以後面就不會監控到，可以查看 Grafana 的log 格式調整設定檔內容。可參考:[Stages | Grafana Labs](https://grafana.com/docs/loki/latest/clients/promtail/stages/)

loki-stacl.yml
```yaml=
loki:
  enabled: true

promtail:
  enabled: true
  pipelineStages:
  - docker: {}
  - match:
      selector: '{app="nginx"}'
      stages:
      - regex:
          expression: '.*(?P<hits>GET /.*)'
      - metrics:
          nginx_hits: 
            type: Counter
            description: "Total nginx requests"
            source: hits
            config:
              action: inc

fluent-bit:
  enabled: false

grafana:
  enabled: true
  sidecar:
    datasources:
      enabled: true
  image:
    tag: 8.3.5

prometheus:
  enabled: true
  server:
    persistentVolume:
      enabled: false
  alertmanager:
    persistentVolume:
      enabled: false


filebeat:
  enabled: false
  filebeatConfig:
    filebeat.yml: |
      # logging.level: debug
      filebeat.inputs:
      - type: container
        paths:
          - /var/log/containers/*.log
        processors:
        - add_kubernetes_metadata:
            host: ${NODE_NAME}
            matchers:
            - logs_path:
                logs_path: "/var/log/containers/"
      output.logstash:
        hosts: ["logstash-loki:5044"]

logstash:
  enabled: false
  image: grafana/logstash-output-loki
  imageTag: 1.0.1
  filters:
    main: |-
      filter {
        if [kubernetes] {
          mutate {
            add_field => {
              "container_name" => "%{[kubernetes][container][name]}"
              "namespace" => "%{[kubernetes][namespace]}"
              "pod" => "%{[kubernetes][pod][name]}"
            }
            replace => { "host" => "%{[kubernetes][node][name]}"}
          }
        }
        mutate {
          remove_field => ["tags"]
        }
      }
  outputs:
    main: |-
      output {
        loki {
          url => "http://loki:3100/loki/api/v1/push"
          #username => "test"
          #password => "test"
        }
        # stdout { codec => rubydebug }
      }
```

```bash=
helm upgrade --install loki grafana/loki-stack -f loki-stack.yml
```

接下來用 Grafana 查詢 Promethus 看有沒有抓到資料，有資料代表成功了。原本以為可能要加時間控制抓5分鐘內，但我後來發現好像不需要做這件事，通知會在超過時間之前就發解決，可能後續我在看文件有沒有預設會抓多久。

![](https://i.imgur.com/szhUj0b.png)

![](https://i.imgur.com/pQQbw19.png)



## 架設 alertmanger-discord

建立 deployment 和 service 後，設定好 webhook 就能使用了。

```yaml=
apiVersion: apps/v1
kind: Deployment
metadata:
  name: alertmanager-discord-deployment
  labels:
    app: alertmanager-discord
spec:
  replicas: 1
  selector:
    matchLabels:
      app: alertmanager-discord
  template:
    metadata:
      labels:
        app: alertmanager-discord
    spec:
      containers:
      - name: alertmanager-discord
        image: benjojo/alertmanager-discord
        ports:
        - containerPort: 9094
        env:
          - name: DISCORD_WEBHOOK
            value: https://discord.com/api/webhooks/***

---
apiVersion: v1
kind: Service
metadata:
  name: alertmanager-discord
spec:
  selector:
    app: alertmanager-discord
  ports:
    - protocol: TCP
      port: 9094
```


可以`kubectl get svc`查看 service 的IP，可以簡單做`curl IP:9094`看有沒有失敗訊息，如果有可以確認 Label 有沒有打錯字，可以查看 endpoint 是不是有IP。




## 設定 alertmanger 通知

可以參考:[helm-charts/values.yaml at main · prometheus-community/helm-charts](https://github.com/prometheus-community/helm-charts/blob/main/charts/alertmanager/values.yaml#L144)

我查看裡面有`Kuberetes@CRDS`可以用，但我這個還不熟悉，之後在接觸。感覺用這個就不需要設定檔案，之前Traefik 有用到 CRDS。
可看[prometheus-operator/alerting.md at main · prometheus-operator/prometheus-operator](https://github.com/prometheus-operator/prometheus-operator/blob/main/Documentation/user-guides/alerting.md)，之後會做簡單嘗試。

其實會寫對照設定還滿簡單的，查看我之前在動物機設定，根本可以抄。

動物機設定
```yaml=
global:
  #163服务器
  smtp_smarthost: 'smtp.qq.com:465'
  #发邮件的邮箱
  smtp_from: 'your-email@foxmail.com'
  #发邮件的邮箱用户名，也就是你的邮箱　　　　　
  smtp_auth_username: 'your-email@foxmail.com'
  #发邮件的邮箱密码
  smtp_auth_password: 'your-password'
  #进行tls验证
  smtp_require_tls: true

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 10s
  repeat_interval: 60m
  receiver: discord_webhook

receivers:
- name: 'discord_webhook'
  webhook_configs:
  - url: 'http://192.168.1.203:9094'

```

loki-stack.yml 的 prometheus 設定

```yaml=
prometheus:
  enabled: true
  server:
    persistentVolume:
      enabled: false
  alertmanager:
    config:
      receivers:
        - name: 'discord_webhook'
          webhook_configs:
          - url: 'http://alertmanger-discord:9094'
    persistentVolume:
      enabled: false

```

## 設定 Prometheus 監控自訂的 metrics

官方建議使用` alerting_rules.yml`去做設定，不建議使用`alerts`。
參考:[helm-charts/values.yaml at main · prometheus-community/helm-charts](https://github.com/prometheus-community/helm-charts/blob/main/charts/prometheus/values.yaml#L1398)

但應該也是有相對應 CRD 去做調整。因為我還沒學到那邊，我覺得我先不使用這方式去做。

```yaml=
prometheus:
  enabled: true
  server:
    persistentVolume:
      enabled: false
  serverFiles:
    alerting_rules.yml: 
      groups:
        - name: example
          rules:         
          - alert: nginx_hits
            expr: promtail_custom_nginx_hits
            for: 30s
            annotations:
              summary: "Instance {{ $labels.instance }} nginx hits"
              description: "{{ $labels.instance }} of job {{ $labels.job }} has been nginx hits for more than 30 seconds."
  alertmanagerFiles:
    alertmanager.yml:
      receivers:
        - name: 'default-receiver'
          webhook_configs:
            - url: 'http://alertmanager-discord:9094'
  persistentVolume:
    enabled: false
```

這邊我們達成使用 Loki 針對 Nginx 訪問直接做跳出提醒，做出簡單很廢的實例，這邊可以改寫正規方法，可以抓到自己想要錯誤內容，使用上難度也沒很高。

## DEBUG

蒐集網路上一些 debug 方法:
- [alertmanager - HackMD](https://hackmd.io/@fwfly/SJe1JiGwU)
可以打 Alertmanager 測試看看，可以看看 discord 有沒有收到通知。沒有可能要繼續找...，我後來發現是我 alertmanagerFile: 裡面 service 打錯，所以沒有收到通知。😅
```bash=
alerts1='[
  {
    "labels": {
       "alertname": "test name",
       "severity" : "Warning"
     },
     "annotations": {
        "Threshold": "<= 2",
        "dashboard": "test",
        "description":"test",
        "infoURL":"test",
        "summary":"this is a test mail",
        "value":"2"
      }
  }
]'
curl -XPOST -d "$alerts1" http://<alertmanager url>/api/v1/alerts
```

- [Prometheus 搭配 Alertmanager 發送警示 - Yowko's Notes](https://blog.yowko.com/prometheus-alertmanager/)
修改 `proxy_url` 設定，可以使用 Fiddler 等等相關工具去調整。我目前 Alertmanger 沒有問題，看到這種 debug 特別紀錄一下。
```yaml=
    receivers:
    - name: 'null' # 不做事
    - name: 'email'
      email_configs: # 指定 email config 來處理
      - to: 'xxx@yowko.com'
    - name: 'webhook'
      webhook_configs: # 指定 webhook config 來處理
      - url: 'http://blog.yowko.com'
        http_config:
          proxy_url: 'http://192.168.80.3:8866' # proxy 是個人 debug 用途
```

## 最後完成 loki-stack.yml(helm value.yml)

把最後實作重新貼到這邊

```yaml=
loki:
  enabled: true

promtail:
  enabled: true
  pipelineStages:
  - match:
      selector: '{app="nginx"}'
      stages:
      - regex:
          expression: '.*(?P<hits>GET /.*)'
      - metrics:
          nginx_hits: 
            type: Counter
            description: "Total nginx requests"
            source: hits
            config:
              action: inc

fluent-bit:
  enabled: false

grafana:
  grafana.ini:
    server:
      root_url: "http://localhost:3000/grafana"
  enabled: true
  sidecar:
    datasources:
      enabled: true
  image:
    tag: 8.3.5

prometheus:
  enabled: true
  server:
    persistentVolume:
      enabled: false
  serverFiles:
    alerting_rules.yml: 
      groups:
        - name: example
          rules:         
          - alert: nginx_hits
            expr: promtail_custom_nginx_hits
            for: 30s
            annotations:
              summary: "Instance {{ $labels.instance }} nginx hits"
              description: "{{ $labels.instance }} of job {{ $labels.job }} has been nginx hits for more than 30 seconds."
  alertmanagerFiles:
    alertmanager.yml:
      receivers:
        - name: 'default-receiver'
          webhook_configs:
            - url: 'http://alertmanager-discord:9094'
  persistentVolume:
    enabled: false


filebeat:
  enabled: false
  filebeatConfig:
    filebeat.yml: |
      # logging.level: debug
      filebeat.inputs:
      - type: container
        paths:
          - /var/log/containers/*.log
        processors:
        - add_kubernetes_metadata:
            host: ${NODE_NAME}
            matchers:
            - logs_path:
                logs_path: "/var/log/containers/"
      output.logstash:
        hosts: ["logstash-loki:5044"]

logstash:
  enabled: false
  image: grafana/logstash-output-loki
  imageTag: 1.0.1
  filters:
    main: |-
      filter {
        if [kubernetes] {
          mutate {
            add_field => {
              "container_name" => "%{[kubernetes][container][name]}"
              "namespace" => "%{[kubernetes][namespace]}"
              "pod" => "%{[kubernetes][pod][name]}"
            }
            replace => { "host" => "%{[kubernetes][node][name]}"}
          }
        }
        mutate {
          remove_field => ["tags"]
        }
      }
  outputs:
    main: |-
      output {
        loki {
          url => "http://loki:3100/loki/api/v1/push"
          #username => "test"
          #password => "test"
        }
        # stdout { codec => rubydebug }
      }


```


```bash=
helm upgrade --install loki grafana/loki-stack -f loki-stack.yml
```
