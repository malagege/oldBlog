---
title: 使用 Helm 快速搭建 Traefik Ingress Controller
date: 2022-04-03 00:02:27
tags: [kubernetes]
categories:  Kubernetes
---

Traefik Ingress Controller 設定花了不少時間，簡單小記一些東西。

<!--more-->

使用前可以用這個看你的 Kubernetes 支援哪些 API，撰寫前記得看網路上資源是不是有一致。有些版本更新會不一樣。

```bash=
kubectl api-resource
```

文章參照這篇[Install Traefik Ingress Controller on Kubernetes using Helm 3 | Traefik Ingress Kubernetes helm - YouTube](https://www.youtube.com/watch?v=4oWojfB_dMQ)實作。

內容:[Install Traefik Ingress Controller on Kubernetes using Helm 3](https://www.fosstechnix.com/install-traefik-ingress-controller-on-kubernetes-using-helm/)



## 安裝 Helm

```bash=
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# 載入現有的指令
source <(helm completion bash)
```

其他可以參考[Helm | Helm Completion Bash](https://helm.sh/docs/helm/helm_completion_bash/)

## helm 佈署 Traefik

```bash=
# 加入 Repo
helm repo add traefik https://helm.traefik.io/traefik

helm repo update
kubectl create ns traefik-v2

## 這邊後續做 Traefik Ingress，所以後面操作我會使用預設(default) namespace 做操作
helm install --namespace=traefik-v2 \
    traefik traefik/traefik

helm -n traefik-v2 get values traefik  -a    

    
helm -n traefik-v2 upgrade traefik traefik/traefik  --set service.type=NodePort  --set ports.traefik.expose=true


#--set rbac.namespaced=true --set providers.kubernetesCRD.allowCrossNamespace=true

```


這邊我使用`--set `去設定 values.yml，後面也可以用 values.yml 去install/upgrade K8s服務，但我目前沒有找到快速建置 values.yml方法，其實我在想是不是 `helm pull xxx` 下來管理檔案室最好作法?後續再研究。


## expose dashboard(測試失敗)

需要看你的namespace 需要補上`-n namespace`。

```
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000
```

不過我上面都會 404，目前沒有測成功...

有查詢相關資料
- [还不会Traefik？看这篇文章就够了！-51CTO.COM](https://network.51cto.com/article/689466.html)
- [Helm Get Values For a Helm Release {Review Revisions}](https://phoenixnap.com/kb/helm-get-values)
- [SSH Tunneling (Port Forwarding) 詳解 · John Engineering Stuff](https://johnliu55.tw/ssh-tunnel.html)
- [RedirectRegex | Traefik | v2.3](https://doc.traefik.io/traefik/v2.3/middlewares/redirectregex/)
- [traefik 9.12.0 · traefik/traefik](https://artifacthub.io/packages/helm/traefik/traefik/9.12.0)
- `http://192.168.0.6:8080/api/http/routers`，可用相關API路徑程式查看有哪些Service，因為。



## 設定 ingress(prefix)

這邊我因為測試，所以把traefik 用在 default namespace。

很多範例都有講到，但很多都是 ingress 設定，多子網域設定，但沒有講道 prefix 設定。

我原本有找到 prefix，但發現倒到 nginx-web pod 都會 404 。
後來我仔細看一下，多了這個`nginx.ingress.kubernetes.io/rewrite-target: /`，以下是官方 nginx 設定。
```yaml=
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
```

Traefik v1 也是用 rewrite，但是 v2 就沒有看到 rewrite 關鍵字，後來找到 `stripPrefix ` 關鍵字才找到方法， 參考:[Introduction to Traefik v2 Ingress Controller in Jelastic Kubernetes | Jelastic](https://jelastic.com/blog/kubernetes-traefik-ingress-controller/)。


```yaml=
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: traefik-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: traefik
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    traefik.ingress.kubernetes.io/router.middlewares: default-testpath-prefix@kubernetescrd
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
```


簡單記錄重點:

1. 設定 Middleware 和 Ingress
2. Ingress 上的 `traefik.ingress.kubernetes.io/router.middlewares` 需要設定`namespace`-`middleware`@kubernetescrd
3. Middleware 需要設定 名稱(如:testpath-prefix)
4. prefixes 要去掉路徑做設定

### 強制使用 TLS 連線

```yaml=
traefik.ingress.kubernetes.io/router.tls: "true"
```


### 設定別的 namespace (測試失敗)

可能用[Service · Kubernetes 中文指南——云原生应用架构实战手册](https://jimmysong.io/kubernetes-handbook/concepts/service.html)

Ingress 不能跨 namespace，但有查到 Ingressroute 可以跨 namespace。但我有找到用 workaound 方法去解決跟 namespace 方法，是使用 ExtenalName ，可參考[simple demo on routing cross namespace by traefik ingress, docker for mac](https://gist.github.com/dfang/4955be38a122d354d52e160ce88697f8)。

測試失敗...，Traefik做的時候，Ingress 設定不上去 Service。

參考網路文章
- [Ingress traffic | Linkerd](https://linkerd.io/2.10/tasks/using-ingress/)
- [Ingress rules in different Kubernetes namespaces | Vincent-Philippe Lauzon’s](https://vincentlauzon.com/2020/02/11/ingress-rules-in-different-kubernetes-namespaces)
- [simple demo on routing cross namespace by traefik ingress, docker for mac](https://gist.github.com/dfang/4955be38a122d354d52e160ce88697f8)
clusterIP 無法 Ping，然怪我怎麼 Ping都失敗，但其實 curl 都是正常的\
- [Kubernetes网络的iptables模式和ipvs模式支持ping分析 - 小家电维修 - 博客园](https://www.cnblogs.com/lizexiong/p/14776517.html)


### 最後設定 traefik-ingress.yml

這有關最後筆記，相關 Grafana 可以看 Loki配置會一起用

```yaml=
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: traefik-ingress
  namespace: default
  annotations:
    kubernetes.io/ingress.class: traefik
    traefik.ingress.kubernetes.io/router.tls: "true"
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