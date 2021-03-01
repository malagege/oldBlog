---
title: Kubernetes 的 ReplicaSet
date: 2021-02-13 02:10:07
tags: [kubernetes,replicasset]
categories: Kubernetes
---

我目前看書上是舊版範例
所以使用官方[ReplicaSet | Kubernetes](https://kubernetes.io/zh/docs/concepts/workloads/controllers/replicaset/#how-a-replicaset-works)範例
目前推薦 [[Kubernetes] ReplicaSet 介紹 | 小信豬的原始部落](https://godleon.github.io/blog/Kubernetes/k8s-ReplicaSet-Overview/)
這邊只是會放操作紀錄，不會很專業解說

<!--more-->

## 前言

一般建立 pod 完可以在建立 service
我以為 ReplicaSet 也是差不多樣子
但實作完才發現不一樣...
建立 ReplicaSet 就會建立 Pod

## 建立有 template 的 ReplicaSet

```yml fronted.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```

```bash
# 建立 fronted ReplicaSet
kubectl apply -f https://kubernetes.io/examples/controllers/frontend.yaml
# replicaset.apps/frontend created
# 查看狀況
kubectl get rs
# NAME       DESIRED   CURRENT   READY   AGE
# frontend   3         3         3       25s
kubectl describle rs/fronted
# Name:         frontend
# Namespace:    default
# Selector:     tier=frontend
# Labels:       app=guestbook
#               tier=frontend
# Annotations:  <none>
# Replicas:     3 current / 3 desired
# Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
# Pod Template:
#   Labels:  tier=frontend
#   Containers:
#    php-redis:
#     Image:        gcr.io/google_samples/gb-frontend:v3
#     Port:         <none>
#     Host Port:    <none>
#     Environment:  <none>
#     Mounts:       <none>
#   Volumes:        <none>
# Events:
#   Type    Reason            Age   From                   Message
#   ----    ------            ----  ----                   -------
#   Normal  SuccessfulCreate  53s   replicaset-controller  Created pod: frontend-bfdqg
#   Normal  SuccessfulCreate  53s   replicaset-controller  Created pod: frontend-7rlhd
#   Normal  SuccessfulCreate  53s   replicaset-controller  Created pod: frontend-p54kd
kubectl get pods 
# NAME             READY   STATUS    RESTARTS   AGE
# frontend-b2zdv   1/1     Running   0          6m36s
# frontend-vcmts   1/1     Running   0          6m36s
# frontend-wtsmm   1/1     Running   0          6m36s
```

```bash
kubectl get pods frontend-b2zdv -o yaml
```
查看 meta.ownerReferences 
這邊先跳過
[详解 Kubernetes ReplicaSet 的实现原理 - 面向信仰编程](https://draveness.me/kubernetes-replicaset/)

## 無 template 建立 

```yml pod-rs.yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod1
  labels:
    tier: frontend
spec:
  containers:
  - name: hello1
    image: gcr.io/google-samples/hello-app:2.0

---

apiVersion: v1
kind: Pod
metadata:
  name: pod2
  labels:
    tier: frontend
spec:
  containers:
  - name: hello2
    image: gcr.io/google-samples/hello-app:1.0

```

```bash
kubectl apply -f https://kubernetes.io/examples/pods/pod-rs.yaml
kubectl get pods 
# NAME             READY   STATUS        RESTARTS   AGE
# frontend-7rlhd   1/1     Running       0          10m
# frontend-bfdqg   1/1     Running       0          10m
# frontend-p54kd   1/1     Running       0          10m
# pod1             0/1     Terminating   0          5s
# pod2             0/1     Terminating   0          5s
```

這邊會看到，我們建立的 pod 沒有變建立起來
原因是 ReplicaSet 設定為三，已經建立過了(label 相同)

如果清空，先建立兩個 pod 再 新增 rs
會友不一樣的結果

```
NAME             READY   STATUS    RESTARTS   AGE
frontend-hmmj2   1/1     Running   0          9s
pod1             1/1     Running   0          36s
pod2             1/1     Running   0          36s
```

## 擴展 ReplicaSet

### 指令

```bash
kubectl scale replicasets fronted --replicas=4
```

指令操作可以直接套用
但大多數不推薦這個方式
假如 yaml 重建不會用到
大多數都從文件調整

## kubectl apply 套用

```bash
kubectl apply -f xxx.yaml
```

## 自動擴展


```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: frontend-scaler
spec:
  scaleTargetRef:
    kind: ReplicaSet
    name: frontend
  minReplicas: 3
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
```

```bash
kubectl apply -f https://k8s.io/examples/controllers/hpa-rs.yaml
```

或只打指令`kubectl autoscale rs frontend --max=10 --min=3 --cpu-percent=50`

看完這麼多，官方推薦使用 Deployment 來替代 ReplicaSet 
這單元簡單操作並不難


## 刪除 ReplicaSet 不刪除 pod

刪除 ReplicaSet 會一起刪除 pod
要刪除 ReplicaSet 不刪除 pod 情況下要使用

```bash
# 書上這樣寫，但好像廢棄了
kubectl delete rs fronted --cascade=false
# warning: --cascade=false is deprecated (boolean value) and can be replaced with --cascade=orphan.
kubectl delete rs frontend --cascade=orphan
# 也能正常運作
```

[StatefulSet介绍 - taotaozh - 博客园](https://www.cnblogs.com/hsyw/p/14173542.html)
不知道跟這個有沒有關係
暫時不研究