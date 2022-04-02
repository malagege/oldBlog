---
title: 使用 Helm 快速安裝 Loki
date: 2022-04-03 00:04:25
tags: [kubernetes,loki]
categories: Kubernetes
---


參考:[kubernetes部署loki日志系统_willblog的博客-CSDN博客_k8s安装loki](https://blog.csdn.net/networken/article/details/120491802)

<!--more-->




```bash=
helm upgrade --install loki grafana/loki-stack \
  --namespace=loki-stack \
  --create-namespace \
  --set grafana.enabled=true \
  --set prometheus.enabled=true \
  --set prometheus.server.persistentVolume.enabled=false \
  --set prometheus.alertmanager.persistentVolume.enabled=false  
  # 假如有設定反向代理需要注意
  # --set grafana.grafana\\.ini.server.root_url='http://localhost:3000/grafana'


# 設定 Grafana Service 為 NodePort
kubectl -n loki-stack patch svc loki-grafana -p '{"spec": {"type": "NodePort"}}'

# 登入 Grafana 密碼\
kubectl get secret --namespace loki-stack loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

```

這邊快速完成佈置。後面有在 value.yml 配置，可參考後面筆記。

##  rewrite/prefix 反向代理設定

做反向代理做 rewrite/prefix 可能需要注意的事情，因為 `Prefix` 到 ReverseProxy 會用一般 url 連到 Server，可以看到下圖會比較清楚，但很多網站大多數都會做 `domain/path/` 前面會加`domain(ip)`，沒強制是還好，如`./dir/a.html` 沒固定連結，但這種也不是很好的做法，大多網站都會強制固定link連結。

{% mermaid graph LR %}

    Client -->|http://ip/prometheus| ReverseProxy
    ReverseProxy --> |http://ip/| Server
    Server --> |redirect http://ip/login | ReverseProxy
    ReverseProxy --> | http://ip/login|Client
{% endmermaid%}


官網範例，因為我不是用 ingress ，我是用 Traefik 來做反向代理，所以就不需要寫這一段

```yaml=
# 官網範例
# https://github.com/grafana/helm-charts/tree/main/charts/grafana
ingress:
  enabled: true
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"

  path: /grafana/?(.*)
  hosts:
    - k8s.example.dev

grafana.ini:
  server:
    root_url: http://localhost:3000/grafana # this host can be localhost
```



因為我不是用 nginx controller ，所以我就稍微改了一下方法，上面nginx controller 我不需要設定。
```bash=
helm upgrade --install loki grafana/loki-stack \
  --set grafana.enabled=true \
  --set prometheus.enabled=true \
  --set prometheus.server.persistentVolume.enabled=false \
  --set prometheus.alertmanager.persistentVolume.enabled=false  \
  --set grafana.grafana\\.ini.server.root_url='http://localhost:3000/grafana'
```

traefik-ingress.yml
```yaml=
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: traefik-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: traefik
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    traefik.ingress.kubernetes.io/router.middlewares: default-testpath-prefix@kubernetescrd,default-grafana-prefix@kubernetescrd
spec:
  rules:
    - http:
        paths:
        - pathType: Prefix
          path: "/testpath"
          backend:
            service:
              name: nginx
              port:
                number: 80
    - http:
        paths:
        - pathType: Prefix
          path: "/grafana"
          backend:
            service:
              name: loki-grafana
              port:
                number: 80

---
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: testpath-prefix
  namespace: default
spec:
  stripPrefix:
    prefixes:
      - /testpath

---
apiVersion: traefik.containo.us/v1alpha1
kind: Middleware
metadata:
  name: grafana-prefix
  namespace: default
spec:
  stripPrefix:
    prefixes:
      - /grafana
```


這邊因為我 ingress 跨 namespace 會抓不到 Server，聽說 ingressroute 可以跨 namespace，相同 namespace 沒有這個問題，所以我先用相同 namespace。

### 彩蛋

[使用 Loki 收集 Traefik 日志 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1824991)

[helm-charts/charts/grafana at main · grafana/helm-charts](https://github.com/grafana/helm-charts/tree/main/charts/grafana)


## traefik 打開一般 log

command 加上設定就能執行，參考:[使用 Loki 收集 Traefik 日志 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1824991)
。

```yaml=
containers:
- args:
  - --accesslog=true
  - --accesslog.format=json
```

helm traefik 設定

```bash=
helm upgrade traefik traefik/traefik  --set service.type=NodePort  --set logs.access.enabled=true
```


## Grafana 可以查詢


![](https://i.imgur.com/TyzmbHK.png)
