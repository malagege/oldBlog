---
title: Kubernetes 操作 Pod 小記
date: 2021-01-19 22:18:36
tags: [pod]
categories: Kubernetes
---

記錄

<!--more-->

使用前建議使用[Install and Set Up kubectl | Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/#enabling-shell-autocompletion)
方便操作指令使用

## 小記知識

K8s 最小運算單原，一個 Pod 可以跑多個 Container。

- Pod
- Pod Controllers

一個 Pod 可以共享的有
- 網路( IP Address, Port , routing system)
- 儲存(可以選擇相同儲存空間)
- IPC(Inter Process Communication)


05:08 補圖(相同網路，內部是127.0.0.1)

Container 狀態
- Running
- Terminated 執行完成(成功或失敗)
- Waiting 還沒跑起來

Pod 狀態
- Pending 還沒有起來
- Running 程式運行中
- Successed 執行成功
- Failed  執行失敗
- Unknown 

Pods 重啟機制
- Never
- Always 總是重啟
- OnFailure 失敗重啟


Node Affinity 靠近
Node taints 遠離

Control Plane 不能部屬 Pod (No Schedule)
kubeadm 預設情況下不能部屬 Pod 在 Control Plane 

可以設定標籤(Label,NodeSelector)



## 建立 Pod(Imperative Commands) 

Imperative Commands 通常測試開發才會使用。正式部屬不建議這樣使用。

```bash
kubectl run kuard --generator=run-pod/v1 --image=gcr.io/kuar-demo/kuard-amd64:blue
# Flag --generator has been deprecated, has no effect and will be removed in the future.
```

[kubectl常用命令 | 好好学习的郝](https://www.voidking.com/dev-kubectl/)
[kubectl --generator 参数被弃用问题解决_沧夜-CSDN博客](https://blog.csdn.net/CANGYE0504/article/details/106179563)
[kubectl 的用法约定 | Kubernetes](https://kubernetes.io/zh/docs/reference/kubectl/conventions/#kubectl-run)

啟動完會看到 pods 有成功

```bash
kubectl get pods
# NAME    READY   STATUS    RESTARTS   AGE
# kuard   1/1     Running   0          12s
```

## 運行 Pod

```
kubectl apply -f kuard-pod.yaml 
```

## 移除 Pod

```bash
# 使用
kubectl delete pods kuard
# 刪除 pods
kubectl delete -f kuard-pod.yaml 
```

## 轉發 port

```bash
# 本地轉發
kubectl port-forward kuard 8080:8080    
# Forwarding from 127.0.0.1:8080 -> 8080
# Forwarding from [::1]:8080 -> 8080

# 對外來原 IP 轉發
kubectl port-forward kuard 8080:8080 --address 0.0.0.0 
# Forwarding from 0.0.0.0:8080 -> 8080

```
更多可參考[kubectl Port-Forward - Kubernetes Port Forwarding Guide](https://phoenixnap.com/kb/kubectl-port-forward)


http://`IP`:8080
就能訪問環境頁面


## 查看 log

```bash
kubectl logs kuard
```

## exec 執行程式

```
kubectl exec kuard ifconfig
kubectl exec kuard ip a
```
跟 docker 操作方式差不多

我注意到`kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.`

```bash
kubectl exec kuard -- ls -l
```

## 容器複製檔案

```
kubectl cp  <file-spec-src> <file-spec-dest>
```

[Kubectl Reference Docs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#cp)

使用中有看到錯誤

```bash
kubectl cp kuard:/kuard ./kuard                                                                             ✔  26  23:09:09 
# tar: removing leading '/' from member names
ls
# kuard
```

有看到tar錯誤，後來 ls 有看到檔案
[修復kubectl cp出現tar: Removing leading `/' from member names - 雲+社區 - 騰訊雲](https://cloud.tencent.com/developer/article/1632562)
[Kubectl cp gives "tar: removing leading '/' from member names" warning · Issue #58692 · kubernetes/kubernetes](https://github.com/kubernetes/kubernetes/issues/58692)
原來是 bug ...

## 健康檢查

```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5      # initialDelaySeconds 字段告訴 kubelet 在執行第一次探測前應該等待 5 秒
      periodSeconds: 5            # periodSeconds 字段指定了 kubelet 應該每 5 秒執行一次存活探測

```
在這個配置文件中，可以看到 Pod 中只有一個容器。 
periodSeconds 字段指定了 kubelet 應該每 5 秒執行一次存活探測。
initialDelaySeconds 字段告訴 kubelet 在執行第一次探測前應該等待 5 秒。
kubelet 在容器內執行命令 cat /tmp/healthy 來進行探測。 如果命令執行成功並且返回值為 0，kubelet 就會認為這個容器是健康存活的。 如果這個命令返回非 0 值，kubelet 會殺死這個容器並重新啟動它。

有兩種 Liveness, Readiness 
上面是[配置存活、就绪和启动探测器 | Kubernetes](https://kubernetes.io/zh/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)範例

```
kubectl apply -f https://k8s.io/examples/pods/probe/exec-liveness.yaml
```

liveness,readiness 後面會在做整理

> 如果使用 Liveness Probe，當錯誤發生時 (無回應或回應錯誤)，k8s 會嘗試重新建立 (kill then create) 一個新的容器。

> 如果使用 Readiness Probe，當錯誤發生時，對應的 Service 物件就會將該容器標示為不可使用，所以任何的需求都不會導向該容器。
可先看[Day 24 - 常見問題與建議 (5) - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10193956)

Liveness Probe 啟動時候，web 可能會有延連時間。例如有些程式啟動不可能馬上出現內容
這時候 `initialDelaySeconds` 設定就很重要

Readiness Prob 啟動時候，Service 會算到 Pod 可用，會把當下 Container IP 加到 Service。
[配置 Pod 的 liveness 和 readiness 探針 ‧ Kubernetes Handbook - Kubernetes中文指南/雲原生應用架構實踐手冊 by Jimmy Song(宋淨超)](https://jimmysong.io/kubernetes-handbook/guide/configure-liveness-readiness-probes.html)

## 資源限制

### Request,Limit

[为容器和 Pods 分配 CPU 资源 | Kubernetes](https://kubernetes.io/zh/docs/tasks/configure-pod-container/assign-cpu-resource/)

Minikube需要安裝 metrics-server 

```bash
minikube addons enable metrics-server

kubectl get apiservices
# NAME
# v1beta1.metrics.k8s.io

```

創建一個 namespace 

```bash
kubectl create namespace cpu-example
```

```yaml https://k8s.io/examples/pods/resource/cpu-request-limit.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cpu-demo
  namespace: cpu-example
spec:
  containers:
  - name: cpu-demo-ctr
    image: vish/stress
    resources:
      limits:
        cpu: "1"
      requests:
        cpu: "0.5"
    args:
    - -cpus
    - "2"

```

```bash
kubectl apply -f https://k8s.io/examples/pods/resource/cpu-request-limit.yaml --namespace=cpu-example

kubectl get pod cpu-demo --namespace=cpu-example
# 可以看到 limit 有限制
# 輸出顯示 Pod 中的一個容器的 CPU 請求為 500 milli CPU，並且 CPU 限制為 1 個 CPU。
# resources:
#   limits:
#     cpu: "1"
#   requests:
#     cpu: 500m



NAME       CPU(cores)   MEMORY(bytes)   
cpu-demo   1006m        2Mi     
```

```bash
kubectl delete namespace cpu-example
```

## Volumes

[Volumes | Kubernetes](https://kubernetes.io/docs/concepts/storage/volumes/)


## 其他

相關 YAML API 文件
[Kubernetes API Reference Docs](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/)


## taint

```bash
# 看Taints: 狀態
kubectl describe 
# 移掉 noschedule 狀態(可以在 Control Pane 操作)
kubectl taint node k8s-dev node-role.kubernetes.io/master:NoSchedule-
```


## 建立 Pod(Imperative Object) 

使用 Yaml或 JSON 格式寫設定。

https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.22/


```bash
kubectl create -f xxxx.yml
kubectl edit pod xxxx
# 手動更改 yaml
# (覆蓋檔案會友先前檔案執行的yaml不一致，，不實用)
kubectl replace -f xxxx.yml
kubectl get pod xxxx -o yaml > new.yml
# 修改 new.yml
kubectl replace -f xxxx.yml
```


## 建立 Pod(Declarative Object)

前面提到 `Imperative Object` kubectl replace 會有當下資源不一致，原因kubectl create 不會計紀錄，但是kubectl apply 會記錄你每次傳指令yaml檔案內容，所以kubectl apply 不會有像 replace 一樣問題。

所以釐清kubectl apply 和 kubectl create 還是有差異的，書中提到用 `kubectl apply`就對了XD


## Pod & Pause Container

1. 基於 network  namespace  實作網路隔離化
2. kubeadm 我發現 container 控制元件都是使用 `host` network namespace，所以跟主機IP一樣

Pod 所有container 都是使用第一個建立 container，docker實作如下。

```bash
docker run -d --name=ubuntu1 dersimn/netutils  tail -f /dev/null
docker run -d --name=ubuntu2 --net=container:ubuntu1 dersimn/netutils  tail -f /dev/null

## 下面兩個指令你會看到 IP 都相等
docker exec ubuntu1 ifconfig
docker exec ubuntu2 ifconfig
# eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
#         inet 172.18.0.2  netmask 255.255.0.0  broadcast 172.18.255.255
#         ether 02:42:ac:12:00:02  txqueuelen 0  (Ethernet)
#         RX packets 9  bytes 1046 (1.0 KB)
#         RX errors 0  dropped 0  overruns 0  frame 0
#         TX packets 0  bytes 0 (0.0 B)
#         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
#         inet 127.0.0.1  netmask 255.0.0.0
#         loop  txqueuelen 1000  (Local Loopback)
#         RX packets 0  bytes 0 (0.0 B)
#         RX errors 0  dropped 0  overruns 0  frame 0
#         TX packets 0  bytes 0 (0.0 B)
#         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# ubuntu1 刪掉 container ，ubuntu2 也會連帶刪除

docker rm -f ubuntu1
docker exec ubuntu2 ifconfig
# lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
#         inet 127.0.0.1  netmask 255.0.0.0
#         loop  txqueuelen 1000  (Local Loopback)
#         RX packets 0  bytes 0 (0.0 B)
#         RX errors 0  dropped 0  overruns 0  frame 0
#         TX packets 0  bytes 0 (0.0 B)
#         TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

# 獨立 ip
docker run -d --name=ubuntu3 --net=host  dersimn/netutils
```
也可用[hwchiu/netutils: Docker image contains useful network tools](https://github.com/hwchiu/netutils)

簡短的說，Container網路共用同網卡問題，砍掉1，2就會看不到。
這邊你看到刪除container1 後 container2 IP就會不見，依照這個狀況下，K8S網路連線這是一個很大問題，所以使用`Pause Container`。


Pause Container
Pod 起來就會背景睡眠，CNI會掛載到Pause Container
讓你container 1 掛掉不會影響其他 container 2 網路



## Workload/Pod Controller

1. ReplicaSet
2. Deployment
3. Daemonset
4. StatefulSet
5. Job
6. CronJob

 ### ReplicaSet

 Kubernetes 的 ReplicaSet

 


 ## kubectl plugin

```
/usr/local/bin/kubectl-xxxx

kubectl xxxx
```

Krew 管理 Plugin 套件 

kubectl krew install tree

### Deployment

Kubernetes 的 Deployment