---
title: Kubernetes 的 DaemonSet
date: 2021-02-18 23:11:50
tags: [Kubernetes, daemonset]
categories: Kuberenetes
---

Deployment 和 ReplicaSet 通常使用多個 replica 時候，都是建在各 nodes 隨機安裝。
DaemonSet 可以讓每一台 node 運行 Pod。
感覺這個跟 docker swarm 的 agent mode :global  一樣 [docker swarm 一些指令相關整理 | 程式狂想筆記](https://malagege.github.io/blog/2019/12/07/docker-swarm-%E4%B8%80%E4%BA%9B%E6%8C%87%E4%BB%A4%E7%9B%B8%E9%97%9C%E6%95%B4%E7%90%86/)
立馬實作看看

<!--more-->

## DaemonSet

使用 DaemonSet 會在所有 node 建立 Pod。如先前所講，跟 Docker Swarm 很像的 agent mode :global 很像
也可以針對 nodename 來建立 Pod。

### 後記

確保每個節點都會有一份運行的Pod。

1. Storage daemon 每台機器都要裝
2. CNI daemon
3. 日誌收集
4. 監控收集

taint 的 NoSchedule 可以阻擋 DaemonSet 部屬。

### DaemonSet 概念圖

原有兩台節點(Node)

後面加上一個Node就會部屬Pod

## 建立 DaemonSet 

[examples/9-1-fluentd.yaml at master · kubernetes-up-and-running/examples](https://github.com/kubernetes-up-and-running/examples/blob/master/9-1-fluentd.yaml)

這邊版本太舊
no matches for kind "DaemonSet" in version "extensions/v1beta1
[no matches for kind "DaemonSet" in version "extensions/v1beta1" · Issue #627 · gluster/gluster-kubernetes](https://github.com/gluster/gluster-kubernetes/issues/627)


> notes:
> 
>     changed apiVersion to "apps/v1"
>     added selector


最後還是套用官方文件[DaemonSet | Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

```yml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # this toleration is to have the daemonset runnable on master nodes
      # remove it if your masters can't run pods
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```


```bash
kubectl apply -f https://k8s.io/examples/controllers/daemonset.yaml
```

查看狀況，這邊namespace 要調整

```bash
kubectl describe daemonsets fluentd-elasticsearch --namespace kube-system
# Name:           fluentd-elasticsearch
# Selector:       name=fluentd-elasticsearch
# Node-Selector:  <none>
# Labels:         k8s-app=fluentd-logging
# Annotations:    deprecated.daemonset.template.generation: 1
# Desired Number of Nodes Scheduled: 6
# Current Number of Nodes Scheduled: 6
# Number of Nodes Scheduled with Up-to-date Pods: 6
# Number of Nodes Scheduled with Available Pods: 6
# Number of Nodes Misscheduled: 0
# Pods Status:  6 Running / 0 Waiting / 0 Succeeded / 0 Failed
# Pod Template:
#   Labels:  name=fluentd-elasticsearch
#   Containers:
#    fluentd-elasticsearch:
#     Image:      quay.io/fluentd_elasticsearch/fluentd:v2.5.2
#     Port:       <none>
#     Host Port:  <none>
#     Limits:
#       memory:  200Mi
#     Requests:
#       cpu:        100m
#       memory:     200Mi
#     Environment:  <none>
#     Mounts:
#       /var/lib/docker/containers from varlibdockercontainers (ro)
#       /var/log from varlog (rw)
#   Volumes:
#    varlog:
#     Type:          HostPath (bare host directory volume)
#     Path:          /var/log
#     HostPathType:  
#    varlibdockercontainers:
#     Type:          HostPath (bare host directory volume)
#     Path:          /var/lib/docker/containers
#     HostPathType:  
# Events:
#   Type    Reason            Age    From                  Message
#   ----    ------            ----   ----                  -------
#   Normal  SuccessfulCreate  2m53s  daemonset-controller  Created pod: fluentd-elasticsearch-rqpth
#   Normal  SuccessfulCreate  2m53s  daemonset-controller  Created pod: fluentd-elasticsearch-g5r57
#   Normal  SuccessfulCreate  2m53s  daemonset-controller  Created pod: fluentd-elasticsearch-bzv6c
#   Normal  SuccessfulCreate  2m53s  daemonset-controller  Created pod: fluentd-elasticsearch-sjt9l
#   Normal  SuccessfulCreate  2m53s  daemonset-controller  Created pod: fluentd-elasticsearch-vfs9w
#   Normal  SuccessfulCreate  2m53s  daemonset-controller  Created pod: fluentd-elasticsearch-wxf6v
```


```bash
kubectl get pods -o wide --namespace kube-system   
# NAME                                          READY   STATUS    RESTARTS   AGE     IP           NODE                  NOMINATED NODE   READINESS GATES
# fluentd-elasticsearch-bzv6c                   1/1     Running   0          6m38s   10.244.2.2   kind-control-plane3   <none>           <none>
# fluentd-elasticsearch-g5r57                   1/1     Running   0          6m38s   10.244.0.2   kind-control-plane    <none>           <none>
# fluentd-elasticsearch-rqpth                   1/1     Running   0          6m38s   10.244.1.5   kind-control-plane2   <none>           <none>
# fluentd-elasticsearch-sjt9l                   1/1     Running   0          6m38s   10.244.5.2   kind-worker3          <none>           <none>
# fluentd-elasticsearch-vfs9w                   1/1     Running   0          6m38s   10.244.3.2   kind-worker           <none>           <none>
# fluentd-elasticsearch-wxf6v                   1/1     Running   0          6m38s   10.244.4.2   kind-worker2          <none>           <none>
```

## node 新增一個 Label

```bash
# 查看 nodes 名稱有哪些
kubectl get nodes
# NAME                  STATUS   ROLES    AGE   VERSION
# kind-control-plane    Ready    master   34m   v1.19.1
# kind-control-plane2   Ready    master   33m   v1.19.1
# kind-control-plane3   Ready    master   32m   v1.19.1
# kind-worker           Ready    <none>   32m   v1.19.1
# kind-worker2          Ready    <none>   32m   v1.19.1
# kind-worker3          Ready    <none>   32m   v1.19.1

# 新增一個 label
kubectl label nodes kind-control-plane ssd=true

# 查看 selector ssd=true nodes
kubectl get nodes --selector ssd=true
```

```yml
apiVersion: apps/v1
kind: "DaemonSet"
metadata:
  labels:
    app: nginx
    ssd: "true"
  name: nginx-fast-storage
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        ssd: "true"
    spec:
      tolerations:
      # this toleration is to have the daemonset runnable on master nodes
      # remove it if your masters can't run pods
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      nodeSelector:
        ssd: "true"
      containers:
        - name: nginx
          image: nginx:1.10.0
```


這邊有實作[Day-25 認識DaemonSet - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10251596)
Pod 都沒有出來

不知道是不是 Kind 出問題??

```yml
      tolerations:
      # this toleration is to have the daemonset runnable on master nodes
      # remove it if your masters can't run pods
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
```

這行家這個就能跑了...
在研究什麼原因



## 刪除 DaemonSet 

```bash
kubectl delete daemonset nginx-fast-storage
```


## DaemonSet Tree


DaemonSet -> ControllerRevision -> Pod

DaemonSet因為有ControllerRevision 可以控制版本。但無法做到Replica一樣功能。



### kube-system 有用到 DaemonSet

```bash
kubectl -n kube-system get ds
```

```
NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kindnet      3         3         3       3            3           <none>                   10d
kube-proxy   3         3         3       3            3           kubernetes.io/os=linux   10d
```



# StatefulSets 

有狀態的Pod。

常運用儲存、網路結合。

### Network

StatefulSet -> Pod-1 
-> Pod-2
-> Pod-3

每個Pod都有個自Dns。

### Storage

Pod-1 -> Volume-1

Pod-2 -> Volume-2


### 更新策略

從後面先動作，先關掉再建立Pod。


### Tree
StatefulSet->ControllerRevision->Pod 