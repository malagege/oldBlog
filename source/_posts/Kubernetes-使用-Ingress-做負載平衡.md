---
title: Kubernetes 使用 Ingress 做負載平衡
date: 2021-02-11 16:37:01
tags: [Kubernetes,ingress]
categories: Kubernetes
---

最近我在學習 Kubernetes，我有問我 K8S 裡面使用 load balance 能不能做到 Cookies 第一次指向到某一台 Server
我那時後才學到 Service ，Service 是使用 iptable 所以應該無法做到
我剛看到 Ingress 章節，應該是可以做到我朋友需求
但仔細想想，探討這個問題也應該沒有意義
使用 K8S 應該就是使用微服務架構，探討 Server 會不會掛掉或更新， Pod 都一定會重置

搜為網路上看一下， 單獨 Ingress 不是可以做到，需要一個 Ingress Controller 才能做到(這邊這個好像就是 nginx 這種東西)
不過，Ingress 和 Ingress Controller 

> Ingress 本身並沒有提供負載平衡的功能，還需要透過 Ingress Controller 來實現。Ingress Controller 目前主要支援兩種型別 GCE 與 Nginx，而今天我們將透過 Nginx Ingress Controller 在 Kubernetes Cluster 內部架設 load balancer。

來源: [[Day 19] 在 Kubernetes 中實現負載平衡 - Ingress Controller - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10196261)

**2021-02-04**
我找到我朋友說的`使用 load balance 能不能做到 Cookies 第一次指向到某一台 Server`
專有述語叫做 `Sticky Session`
Nginx Ingress Controller 可以做到
- [Sticky Sessions - NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)
- [Services - Traefik](https://doc.traefik.io/traefik/routing/services/#sticky-sessions)
- [Ingress session sticky_总结、分享、交流-CSDN博客](https://blog.csdn.net/linuxerlin/article/details/107561912)
結論還是能做到的

<!--more-->

## 前言

之前有碰過 traefik ，在使用 docker 情況下 ，traefik 是使用 docker label 去抓設定
我原本以為 K8s 應該也差不多
後來看到 Ingress 跟 Ingress Controller 一直在想這件事情
突然想到 Ingress 應該就控制網站Virtual Server(不確定是不是這樣叫) 設定
Ingress Controller 可能是 Apache , nginx (類似這些東西做反向代理)
這樣換不同 Ingress Controller 也不用重新設定
應該是這樣定位吧(我自己想的)

教學還是建議看 [[Day 19] 在 Kubernetes 中實現負載平衡 - Ingress Controller - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10196261)
我這邊還是以操作紀錄為主

Kind 由於先前發現做 NodePort 會有問題，這邊用 minikube 可能比較不會出狀況
當然官方也是有方法可以避免
可以看一下官方操作方法[kind – Ingress](https://kind.sigs.k8s.io/docs/user/ingress/)
主要是 expose port

## Kind 官方設定教學

### 對應環境設定 (Kind)

```bash
kind delete cluster # 確定刪除
kubectl config get-context # 確定使用的 k8s 連結設定
cat <<EOF | kind create cluster --config=-
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
EOF
```

### 安裝 Contour (Ingress Controller)

```bash
kubectl apply -f https://projectcontour.io/quickstart/contour.yaml
# namespace/projectcontour created
# serviceaccount/contour created
# serviceaccount/envoy created
# configmap/contour created
# customresourcedefinition.apiextensions.k8s.io/extensionservices.projectcontour.io created
# customresourcedefinition.apiextensions.k8s.io/httpproxies.projectcontour.io created
# customresourcedefinition.apiextensions.k8s.io/tlscertificatedelegations.projectcontour.io created
# serviceaccount/contour-certgen created
# rolebinding.rbac.authorization.k8s.io/contour created
# role.rbac.authorization.k8s.io/contour-certgen created
# job.batch/contour-certgen-v1.12.0 created
# clusterrolebinding.rbac.authorization.k8s.io/contour created
# clusterrole.rbac.authorization.k8s.io/contour created
# service/contour created
# service/envoy created
# deployment.apps/contour created
# daemonset.apps/envoy created
```

### 設定 daemonsets  

```json
{
  "spec": {
    "template": {
      "spec": {
        "nodeSelector": {
          "ingress-ready": "true"
        },
        "tolerations": [
        {
          "key": "node-role.kubernetes.io/master",
          "operator": "Equal",
          "effect": "NoSchedule"
        }
        ]
      }
    }
  }
}

```

```bash
kubectl patch daemonsets -n projectcontour envoy -p '{"spec":{"template":{"spec":{"nodeSelector":{"ingress-ready":"true"},"tolerations":[{"key":"node-role.kubernetes.io/master","operator":"Equal","effect":"NoSchedule"}]}}}}'
```

先設定，之後代研究這個是做什麼用
但我發現這個沒設定好像也能跑 = =a

### 使用 Ingress 

預設好像就有這個，所以不需要特別裝

```yml 
kind: Pod
apiVersion: v1
metadata:
  name: foo-app
  labels:
    app: foo
spec:
  containers:
  - name: foo-app
    image: hashicorp/http-echo:0.2.3
    args:
    - "-text=foo"
---
kind: Service
apiVersion: v1
metadata:
  name: foo-service
spec:
  selector:
    app: foo
  ports:
  # Default port used by the image
  - port: 5678
---
kind: Pod
apiVersion: v1
metadata:
  name: bar-app
  labels:
    app: bar
spec:
  containers:
  - name: bar-app
    image: hashicorp/http-echo:0.2.3
    args:
    - "-text=bar"
---
kind: Service
apiVersion: v1
metadata:
  name: bar-service
spec:
  selector:
    app: bar
  ports:
  # Default port used by the image
  - port: 5678
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - http:
      paths:
      - path: /foo
        backend:
          serviceName: foo-service
          servicePort: 5678
      - path: /bar
        backend:
          serviceName: bar-service
          servicePort: 5678
---
```

上面建立兩個pod,service，然後新增 Ingress 設定

```bash
kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/usage.yaml
```

### 執行內容

```bash
# should output "foo"
curl localhost/foo
# should output "bar"
curl localhost/bar
```

### 查看 Ingress 指令

```bash
kubectl get ingress
kubectl describle ingress simple-ingress
```

### kubernetes之ingress error: endpoints "default-http-backend" not found 

```
翻譯：
控制器。注意:根據您正在使用的Ingress控制器，您可能需要創建一個default-http-backend服務。
違約後端
沒有規則的入口將所有流量發送到一個默認後端。默認後端通常是Ingress控制器的一個配置選項，在您的Ingress資源中沒有指定。
如果Ingress對象中的主機或路徑都不匹配HTTP請求，則流量將被路由到默認後端。
```

參考:[kubernetes之ingress error: endpoints "default-http-backend" not found - Mr.zou - 博客园](https://www.cnblogs.com/zoulixiang/p/13554893.html)


## 使用 host 名稱對應 service

由於書中跟 [[Day 19] 在 Kubernetes 中實現負載平衡 - Ingress Controller - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10196261)
為了加速實作，這邊使用上面範例

### 使用 minikube

```bash
# 啟用 minikube 的 ingress
minikube addons enable ingress
# 取得 minikube ip
minikube ip
```

### service ,pod , ingress 設定

```bash
# 建立測試 pod
kubectl create -f  https://raw.githubusercontent.com/zxcvbnius/k8s-30-day-sharing/master/Day19/demo-ingress/helloworld-pod.yaml
```

事後想想這邊可以用上面範例 echo 出不同字串
這樣就可以知道用到哪一個 pod


建立兩個測試用 serivce 都倒同一個 service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: helloworld-v1
spec:
  ports:
  - port: 3000
    protocol: TCP
    targetPort: 3000
  selector:
    app: helloworld-pod
```

```yml
apiVersion: v1
kind: Service
metadata:
  name: helloworld-v2
spec:
  ports:
  - port: 3000
    protocol: TCP
    targetPort: 3000
  selector:
    app: helloworld-pod
```

```bash
kubectl create -f https://raw.githubusercontent.com/zxcvbnius/k8s-30-day-sharing/master/Day19/demo-ingress/helloworld-service.yaml
kubectl get svc
# Name:              helloworld-v1
# Namespace:         default
# Labels:            <none>
# Annotations:       <none>
# Selector:          app=helloworld-pod
# Type:              ClusterIP
# IP Families:       <none>
# IP:                10.96.172.11
# IPs:               <none>
# Port:              <unset>  3000/TCP
# TargetPort:        3000/TCP
# Endpoints:         172.17.0.7:3000
# Session Affinity:  None
# Events:            <none>


# Name:              helloworld-v2
# Namespace:         default
# Labels:            <none>
# Annotations:       <none>
# Selector:          app=helloworld-pod
# Type:              ClusterIP
# IP Families:       <none>
# IP:                10.96.251.221
# IPs:               <none>
# Port:              <unset>  3000/TCP
# TargetPort:        3000/TCP
# Endpoints:         172.17.0.7:3000
# Session Affinity:  None
# Events:            <none>
```

確認是否成功

```bash
# 進去 node 確認狀況
minikube ssh
# 確認 pods 可以正常連到
curl 10.96.172.11:3000
```

繼續建立 ingress controller

```bash
# 官方安裝 nginx ingress controller
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.44.0/deploy/static/provider/baremetal/deploy.yaml
# 原作yaml 無法建立
# error: error validating "https://raw.githubusercontent.com/zxcvbnius/k8s-30-day-sharing/master/Day19/demo-ingress/nginx-ingress.yaml": error validating data: [ValidationError(Ingress.metadata): unknown field "kubernetes.io/ingress.class" in io.k8s.apimachinery.pkg.apis.meta.v1.ObjectMeta, ValidationError(Ingress.spec.rules[0]): unknown field "paths" in io.k8s.api.extensions.v1beta1.IngressRule]; if you choose to ignore these errors, turn validation off with --validate=false
vim nginx-ingress.yaml 
```
參考 [k8s-30-day-sharing/ingress-example-2.yaml at master · zxcvbnius/k8s-30-day-sharing](https://github.com/zxcvbnius/k8s-30-day-sharing/blob/master/Day19/demo-ingress/ingress-example-2.yaml)
裡面 service name 有錯誤，建議使用下面複製
```yml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-2
spec:
  rules:
  - host: helloworld-v1.example.com
    http:
      paths:
      - path: /
        backend: 
          serviceName: helloworld-v1
          servicePort: 80
  - host: helloworld-v2.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: helloworld-v2
          servicePort: 80
```

### 測試 host 方法

1. curl -H
之前[在 Raspberry PI 架設 Traefik 初體驗 | 程式狂想筆記](https://malagege.github.io/blog/2020/10/13/%E5%9C%A8-Raspberry-PI-%E6%9E%B6%E8%A8%AD-Traefik-%E5%88%9D%E9%AB%94%E9%A9%97/#%E6%B8%AC%E8%A9%A6%E6%96%B9%E6%B3%95)這篇有紀錄
通常改 /etc/hosts 檔案或者curl 指令可以做到(這當初看到 traefik 官方文件寫的，還滿神奇的，畢竟很多教學沒這樣教)


2. /etc/hosts

```bash
# 這個 IP 是 node IP
curl -H Host:helloworld-v1.example.com http://192.168.99.102/  
curl -H Host:helloworld-v2.example.com http://192.168.99.102/  
```


## SSL termination(代理端做 tls)

ingress-ssl-sceret.yaml
```yaml ingress-ssl-sceret.yaml
apiVersion: v1
data:
  tls.crt: YmFzZTY0X2VuY29kZWRfY2VydA==
  tls.key: YmFzZTY0X2VuY29kZWRfa2V5
kind: Secret
metadata:
  name: ssh-secret
  namespace: default
type: Opaque
```

example3-ingress.yaml
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-3
spec:
  tls:
  - secretName: ssh-secret
  backend:
    serviceName: helloworld-v1
    servicePort: 3000
```


## 運行多個 Ingress Controller

一個叢集中運行多個Ingress 控制器，可以搭配 Annotation 的 `kubernetes.io/ingress.class`
如果沒有配置 `kubernetes.io/ingress.class`，會有多個不同Ingress Controller 搶 Ingress 使用權，並寫入Ingress 件的status 欄位



後面兩個就不先仔細研究

## wildcard host

[Ingress | Kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/#hostname-wildcards)

## rewrite url

[ingress-nginx 中 Rewrite 的使用-阳明的博客|Kubernetes|Istio|Prometheus|Python|Golang|云原生](https://www.qikqiak.com/post/url-rewrite-on-ingress-nginx/)


## 感想

之前有操作 Traefik 使用 Docker Label 讓 Traefik 可以自動抓到想要的 host 
使用 Ingress 來看，這個設計能做到模組化分離，可算非常棒!!

以後有用到 Traefik  Ingress 再回來填坑