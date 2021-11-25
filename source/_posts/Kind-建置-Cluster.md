---
title: Kind 建置 Cluster
date: 2021-02-18 22:30:59
tags: [Kubernetes]
categories: Kuberenetes
---

最近看 Kuberenets 看到 DaemonSet，由於目前 Kind 跟 MiniKube 都是使用單結點，沒辦法用 Cluster，今天照 Kind 實作看看。

<!--more-->

## 安裝 Kind

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
chmod +x ./kind
mv ./kind /some-dir-in-your-PATH/kind

```
可以看[kind – Quick Start](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)

## 建置 Cluster


```bash
# 預設 cluster context 名稱為 `kind`.
kind create cluster 

# 名稱為 kind-2
kind create cluster --name kind-2
```

這邊建立出來會有兩個 Cluster
但這兩個 Cluster 是分開的

等等，我不是要分開的

## 查看哪些 Cluster

```bash
kind get clusters
# kind
# kind-2
kubectl cluster-info --context kind-kind
kubectl cluster-info --context kind-kind-2
```

等等，我不是要分開的

## 刪除 Cluster

```bash
kind delete cluster --name kind-2
```

## Multi-node clusters

```yml
# three node (two workers) cluster config
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

```yml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: control-plane
- role: control-plane
- role: worker
- role: worker
- role: worker
```

上面 yml 選一個跑

```bash
kind create cluster --config kind-example-config.yaml
```


建立這麼簡單
感覺跟 Docker Swarm 一樣

我以為要注意在連 Cluster 看當下 node 有哪些 container
看來全部都合起來了



看完，真的是白擔心了
真的可以單結點來測試

不過，DaemonSet 好像能控制在哪個 nodes 執行
建立 Cluster 多 node 也不錯


**發現重開機，kubectl 就會連不到**
所以，應該只能重建