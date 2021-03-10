---
title: Kubernetes 服務探索(Service Discovery)
date: 2021-02-04 21:48:46
tags: [Kubernetes,Service]
categories: Kubernetes
---

Kubernetes  Service 筆記

<!--more-->

```bash
kubectl run alpaca-prod \
--image=gcr.io/kuar-demo/kuard-amd64:blue \
--port=8000 \
--labels="ver=1,app=alpaca,env=prod"

# 用 expose 創建一個 service
kubectl expose pod alpaca-prod

# 這邊很有趣，看 kubectl describe svc alpaca-prod
# 裡面
# spec:
#   clusterIP: 10.96.29.232
#   ports:
#   - port: 8000
#     protocol: TCP
#     targetPort: 8000
#   selector:
#     app: alpaca
#     env: prod
#     ver: "1"
#是用 selector 對印

kubectl get services -o wide

```

```bash
# 現在 kubectl run 產生 pod ，所以應該可以不用這段
# 這段是書寫的，之後改成 deployment 再嘗試
ALPACA_POD=$(kubectl get pods -l app=alpaca \
-o jsonpath='{.items[0].metadata.name}')

# $ALPACA_POD => alapaca-pod
kubectl port-forward $ALPACA_POD 48858:8080

# 執行完成可以用 localhost:48858 看到頁面
```



## Cluster IP

Cluster IP 是虛擬 IP，通常會指定一個 DNS，通常 IP 不會更動，這樣就不會有 DNS 快取問題。
在 namespace ，我們只需要用 service 名稱連結 pod

### Service DNS

用剛剛`kubectl port-forward $ALPACA_POD 48858:8080` 
進去`http://localhost:48858/`裡面的`DNS QUERY`
name 輸入 `alpaca-prod`就可以找到

[Debugging DNS Resolution | Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)

alpaca-prod.default.svc.cluster.local

這邊可以看到 `alpaca-prod`後面 `default` 是 namespace
可以在 dns 搜尋 alpaca-prod.default 查詢到東西
完整的`alpaca-prod.default.svc.cluster.local`
也可以查詢到

```
;; opcode: QUERY, status: NOERROR, id: 22467
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;alpaca-prod.default.svc.cluster.local.	IN	 A

;; ANSWER SECTION:
alpaca-prod.default.svc.cluster.local.	30	IN	A	10.99.141.81
```


### Readiness Prob

```
kubectl  edit deployment/alpaca-prod
```
由於這邊使用 pod 會有`Forbidden: pod updates may not change fields other than`
> Kubernetes 對 Pod 的更新做了限制，除了更改 Pod 中容器（包括工作容器與初始化容器）的鏡像，以及 activeDeadlineSeconds （對 Job 類型的 Pod 定義失敗重試的最大時間）， tolerations （Pod 對污點的容忍），修改其它部分將不會產生作用，如我們可以嘗試在前面 Pod 定義文檔 pod-test.yaml 中將宿主機端口 8081 改為 8082，重新執行 kubectl apply， 將提示如下錯誤，

參考:[Kubernetes筆記（五）：瞭解Pod（容器組） - 知乎](https://zhuanlan.zhihu.com/p/234793180)

```yaml
...
    livenessProbe:
      httpGet:
        path: /
        port: 80
        httpHeaders: # 此處header無意義，僅作示例
        - name: purpose
          value: for-test
      initialDelaySeconds: 2
      periodSeconds: 5
```
這邊就先不仔細研究


### 用 endpoints 觀察
使用 endpint 是尋找 service 將流量發送到何處地方

```
kubectl get endpoints alpaca-prod --watch
```


## NodePort

```
kubectl edit service alpaca-prod
```

TYPE 改成 NodeType
這個就跟 docker 設定 port 對外很像

```
Name:                     alpaca-prod
Namespace:                default
Labels:                   app=alpaca
                          env=prod
                          ver=1
Annotations:              <none>
Selector:                 app=alpaca,env=prod,ver=1
Type:                     NodePort
IP Families:              <none>
IP:                       10.99.141.81
IPs:                      <none>
Port:                     <unset>  8000/TCP
TargetPort:               8000/TCP
NodePort:                 <unset>  30306/TCP
Endpoints:                10.244.0.6:8000
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

這邊注意，由於我們使用 Kind 做測試
使用 nodePort 發現怎麼連不到
後來我注意到 node ，不應該是指操作 kubectl 主機上 面

```
kubectl get node -o wide
```

但是對 node 主機上 port 還是連不到

```
ssh <ndoe_ip> -L 8080:localhost:30306
```

不過看來 kind 不支源使用 nodeport
- [docker - How to use NodePort with kind? - Stack Overflow](https://stackoverflow.com/questions/62432961/how-to-use-nodeport-with-kind)
- [nginx - Kubernetes NodePort not listening - Stack Overflow](https://stackoverflow.com/questions/63124127/kubernetes-nodeport-not-listening)

在考慮之後用 vagrant 環境跑跑看

## LoadBalancer

一般雲端內件有提供這個功能
但好像也能自己架
這邊就不嘗試
[如何建構 Kubernetes loadbalancer – BROBRIDGE](https://brobridge.com/bdsres/2019/10/31/%E5%A6%82%E4%BD%95%E8%87%AA%E5%B7%B1%E5%BB%BA%E6%A7%8B-k8s-loadbalancer/)
[[Day12] 實作 Kubernetes 裸機 Load Balancer Part2 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10221722)


## Endpoint

創建 Service 會一同建立 Endpoint

詳細:[[Kubernetes] Service Overview | 小信豬的原始部落](https://godleon.github.io/blog/Kubernetes/k8s-Service-Overview/)
感覺是 Service 底層操作元件

```bash
kubectl describle endpints alpaca-prod
# Name:         alpaca-prod
# Namespace:    default
# Labels:       app=alpaca
#               env=prod
#               ver=1
# Annotations:  <none>
# Subsets:
#   Addresses:          10.244.0.6
#   NotReadyAddresses:  <none>
#   Ports:
#     Name     Port  Protocol
#     ----     ----  --------
#     <unset>  8000  TCP

# Events:  <none>
```

```bash
kubectl get endpints alpaca-prod --watch
# NAME          ENDPOINTS         AGE
# alpaca-prod   10.244.0.6:8000   6d2h
# alpaca-prod   <none>            6d2h  <--  kubectl delete pods alpaca-prod  
# alpaca-prod   10.244.0.7:8000   6d2h  <--  kubectl run alpaca-prod --image=gcr.io/kuar-demo/kuard-amd64:blue --port=8080 --labels="ver=1,app=alpaca,env=prod"  

```

## 建立 Service yaml


```yml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376
```

簡單來說 Service 對印 selector 去抓含有 Label 的 `Pod`


## kube-proxy

kube-proxy 每一台 node 主機上面都會有這個東西。
kube-proxy 會監測 service 和 endpoint 是否發生變化，會重寫該模組 iptables 規則。

詳細:
- [kube-proxy · Kubernetes指南](https://feisky.gitbooks.io/kubernetes/content/components/kube-proxy.html)
- [kubernetes核心组件kube-proxy - 运维笔记 - 散尽浮华 - 博客园](https://www.cnblogs.com/kevingrace/p/6655153.html){% asset_link web1.png  備份圖 %}

## Cluster IP 環境變數

```bash
kubectl exec alpaca-prod -- env
# PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
# HOSTNAME=alpaca-prod
# KUBERNETES_PORT_443_TCP_PROTO=tcp
# ALPACA_PROD_PORT_8000_TCP_PROTO=tcp
# KUBERNETES_SERVICE_HOST=10.96.0.1
# KUBERNETES_PORT=tcp://10.96.0.1:443
# KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
# ALPACA_PROD_PORT=tcp://10.99.141.81:8000
# ALPACA_PROD_PORT_8000_TCP_PORT=8000
# KUBERNETES_SERVICE_PORT_HTTPS=443
# KUBERNETES_PORT_443_TCP_PORT=443
# ALPACA_PROD_PORT_8000_TCP=tcp://10.99.141.81:8000
# ALPACA_PROD_PORT_8000_TCP_ADDR=10.99.141.81
# KUBERNETES_SERVICE_PORT=443
# KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
# ALPACA_PROD_SERVICE_HOST=10.99.141.81
# ALPACA_PROD_SERVICE_PORT=8000
```

## 清除所有建立物件

```bash
# 查看之前建立有app label 的 pods , services 狀態
kubectl get pods,services  -l app
# 刪除 app label 物件
kubectl delete services,deployments,pods -l app
```
