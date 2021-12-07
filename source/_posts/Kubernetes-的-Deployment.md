---
title: Kubernetes 的 Deployment
date: 2021-02-14 19:14:24
tags: [kubernetes, deployment]
categories: Kubernetes
---

感覺是滿多的功能
但頁數比較想像中的少
趕緊消化

<!--more-->

## 前言

ReplicaSet 單元最後看到使用 Deployment
想說怎麼不直接教 Deployment
實作簡單功能，發現原來 Deployment 會建立 ReplicaSet 
所以還是要會 ReplicaSet 怎麼用比較好


## 建立一個 Deployment

遇到apiVersion
error: unable to recognize "kuard-deployment.yml": no matches for kind "Deployment" in version "extensions/v1beta1"
error: unable to recognize "kuard-deployment.yml": no matches for kind "Deployment" in version "apps/v1beta1"
參考:[kubernetes - no matches for kind "Deployment" in version "extensions/v1beta1" - Stack Overflow](https://stackoverflow.com/questions/58481850/no-matches-for-kind-deployment-in-version-extensions-v1beta1)

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kuard-deployment
#   labels:
#     app: kuard
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kuard
  template:
    metadata:
      labels:
        app: kuard
    spec:
      containers:
        - image: gcr.io/kuar-demo/kuard-amd64:blue
          name: kuard
```

[Kubernetes kuard deployment](https://gist.github.com/erikaulin/d4de231655a1a5011dae5ecc39a49956)


```bash
# 查看所有 Object List
kubectl get all 
# NAME                                    READY   STATUS    RESTARTS   AGE
# pod/kuard-deployment-6cd695495f-kt88w   1/1     Running   0          105s

# NAME                  TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
# service/bar-service   ClusterIP   10.96.108.167   <none>        5678/TCP   2d16h
# service/foo-service   ClusterIP   10.96.109.30    <none>        5678/TCP   2d16h
# service/kubernetes    ClusterIP   10.96.0.1       <none>        443/TCP    2d17h

# NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
# deployment.apps/kuard-deployment   1/1     1            1           106s

# NAME                                          DESIRED   CURRENT   READY   AGE
# replicaset.apps/kuard-deployment-6cd695495f   1         1         1       106s

kubectl get deployments kuard-deployment -o jsonpath --template {.spc.selector.matchLabels}
# {"app":"kuard"}

kubectl get all --selector="app=kuard"
# NAME                                    READY   STATUS    RESTARTS   AGE
# pod/kuard-deployment-6cd695495f-kt88w   1/1     Running   0          9m34s

# NAME                                          DESIRED   CURRENT   READY   AGE
# replicaset.apps/kuard-deployment-6cd695495f   1         1         1       9m34s
kubectl get replicasets --selector=app=kuard
# NAME                          DESIRED   CURRENT   READY   AGE
# kuard-deployment-6cd695495f   1         1         1       79m

# 修改 deployment 的 replicas
kubectl scale deployments kuard-deployment --replicas=3

kubectl get replicasets --selector=app=kuard
# NAME                          DESIRED   CURRENT   READY   AGE
# kuard-deployment-6cd695495f   3         3         3       82m

# 修改 replicaset = 1(這邊不是用 deployment)
kubectl scale replicaset kuard-deployment-6cd695495f --replicas=1 

kubectl get replicasets --selector=app=kuard
# NAME                          DESIRED   CURRENT   READY   AGE
# kuard-deployment-6cd695495f   3         3         3       82m

# 不會發生任何改變，下面等等會說
```

這邊說一下運作方式
Deployment 會管理 ReplicaSet，ReplicaSet 用來管理Pod。
所以要懂 ReplicaSet 是做什麼用的。

上面對 ReplicasSet 做 scale 調整
為什麼不會改變，是因為`Deployment` 上層設定不一樣的值
Deployment 控制器會重新調整 replica 數量

這種情況下，有一個方法改 ReplicaSet 
對 Deployment 刪除(`--cascade=orphan`) <-- 舊版是 `--cascade=false`


```bash
# 匯出設定檔出來
kubectl get deployments kuard-deployment -o yaml > kuard-deployment.yaml
# 暫時不研究，但應該可以不用做
#kubectl replace -f kuard-deployment.yaml --save-config
```

可以看到 裡面有很多預設值

```yaml
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
```

這邊跟書裡面提到不一樣
看來新版`maxSurge`和`maxUnavailable`都變成百分比

## 管理 Deployment

```bash
kubectl describe deployments kuard-deployment
```

```
Name:                   kuard-deployment
Namespace:              default
CreationTimestamp:      Sun, 14 Feb 2021 20:55:44 +0800
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=kuard
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=kuard
  Containers:
   kuard:
    Image:        gcr.io/kuar-demo/kuard-amd64:blue
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   kuard-deployment-6cd695495f (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  16m   deployment-controller  Scaled down replica set kuard-deployment-6cd695495f to 1
```

```
OldReplicaSets:  <none>
NewReplicaSet:   kuard-deployment-6cd695495f (1/1 replicas created)
```

其中 `OldReplicaSets` 和 `NewReplicaSet` 是由 Deployment 管理狀態。rollout 中兩邊欄位值會有資料，rollout 完成後 OldReplicaSets 就會 none。

## 更改容器的映像檔

修改 deployment 設定裡面的 image tag 改成 green

```yml
    spec:
      containers:
        - image: gcr.io/kuar-demo/kuard-amd64:blue
```

`kubectl apply -f kuard-deployment.yml`
建議 replicaset 可以設定大一點
可以看到 OldReplicaSets ,NewReplicaSet 這兩個變化

```bash
# 查看更新狀態
kubectl rollout status deployments kuard-deployment

# 可以看到新舊 ReplicaSets 都存在，之後退版可能會用到
kubectl get replicasets -o wide 
# NAME                          DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES                               SELECTOR
# kuard-deployment-5984cbd7dd   0         0         0       13m     kuard        gcr.io/kuar-demo/kuard-amd64:green   app=kuard,pod-template-hash=5984cbd7dd
# kuard-deployment-6cd695495f   20        20        20      6h38m   kuard        gcr.io/kuar-demo/kuard-amd64:blue    app=kuard,pod-template-hash=6cd695495f
```

~~TODO: 這邊 deployment 程式後，不知道 Service LB 進去會等全部上完好，才會連到新的POD，還是會綜合?~~
建立 4 個 ReplicaSets 去跑
`kubectl get endpoints kuard-service --watch -o yaml`
查看變化，看來**舊新**在跑的時候會同時 LB 舊新(綜合)


```bash
# 暫停部屬
kubectl rollout pause deployments kuard-deployment
# 繼續部屬
kubectl rollout resume deployments kuard-deployment

```

## 查詢歷史狀態

```bash
# 查看 Deployment 的歷史紀錄
kubectl rollout history deployment kuard-deployment
# deployment.apps/kuard-deployment 
# REVISION  CHANGE-CAUSE
# 7         <none>
# 8         <none>
# 9         <none>
# 12        <none>
# 13        <none>
# 查看 Deployment 詳細資訊
kubectl rollout history deployment kuard-deployment --revision=12
# deployment.apps/kuard-deployment with revision #12
# Pod Template:
#   Labels:       app=kuard
#         pod-template-hash=bd94b5dbb
#   Containers:
#    kuard:
#     Image:      gcr.io/kuar-demo/kuard-amd64:blue
#     Port:       8080/TCP
#     Host Port:  0/TCP
#     Environment:        <none>
#     Mounts:     <none>
#   Volumes:      <none>
```

## 退回版本(Rollback)

1. 可以重新修改 deployment.yml 再做 `kubectl apply -f deployment.yml`
2. 可以用`kubectl rollout`指令，如下

```bash
# 退回前一個版本
kubectl rollout undo deployments kuard-deployment
# deployment.apps/kuard-deployment rolled back

# 查看 replica 數
kubectl get replicasets -o wide
# NAME                          DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES                               SELECTOR
# kuard-deployment-5984cbd7dd   0         0         0       22h   kuard        gcr.io/kuar-demo/kuard-amd64:green   app=kuard,pod-template-hash=5984cbd7dd
# kuard-deployment-697c6c85     0         0         0       21h   kuard        gcr.io/kuar-demo/kuard-amd64:green   app=kuard,pod-template-hash=697c6c85
# kuard-deployment-6cd695495f   0         0         0       29h   kuard        gcr.io/kuar-demo/kuard-amd64:blue    app=kuard,pod-template-hash=6cd695495f
# kuard-deployment-bd94b5dbb    4         4         4       21h   kuard        gcr.io/kuar-demo/kuard-amd64:blue    app=kuard,pod-template-hash=bd94b5dbb
# kuard-deployment-f948c99d9    0         0         0       21h   kuard        gcr.io/kuar-demo/kuard-amd64:1       app=kuard,pod-template-hash=f948c99d9

# 退回某一版 --to-revision
kubectl rollout undo deployments kuard-deployment --to-revision=11

# 指令上一個版本 => --to-revision=0
kubectl rollout history deployment kuard-deployment --to-revision=0
```

> 清理策略
> 
> 你可以在 Deployment 中設置 .spec.revisionHistoryLimit 字段以指定保留此 Deployment 的多少個舊有 ReplicaSet。其餘的 ReplicaSet 將在後台被> 垃圾回收。 默認情況下，此值為 10。
> 
> 說明： 顯式將此字段設置為 0 將導致 Deployment 的所有歷史記錄被清空，因此 Deployment 將無法回滾。
滾動更新 Deployment


## Deployment 策略

1. Recreate 策略

會把現有的 ReplicaSet 的 Pod刪掉，全部新建新的Pod。但會使服務中斷，通常不會使用

2. RollingUpdate(預設、重要)

滾動方式不停機情況下更新

## 滾動更新設定

> Deployment 會在 .spec.strategy.type==RollingUpdate時，採取 滾動更新的方式更新 Pods。你可以指定 maxUnavailable 和 maxSurge 來控制滾動更新 過程。
> 最大不可用
> 
> .spec.strategy.rollingUpdate.maxUnavailable 是一個可選字段，用來指定 更新過程中不可用的 Pod 的個數上限。該值可以是絕對數字（例如，5），也可以是 所需 Pods 的百分比（例如，10%）。百分比值會轉換成絕對數並去除小數部分。 如果 .spec.strategy.rollingUpdate.maxSurge 為 0，則此值不能為 0。 默認值為 25%。
> 
> 例如，當此值設置為 30% 時，滾動更新開始時會立即將舊 ReplicaSet 縮容到期望 Pod 個數的70%。 新 Pod 準備就緒後，可以繼續縮容舊有的 ReplicaSet，然後對新的 ReplicaSet 擴容，確保在更新期間 可用的 Pods 總數在任何時候都至少為所需的 Pod 個數的 70%。
> 最大峰值
> 
> .spec.strategy.rollingUpdate.maxSurge 是一個可選字段，用來指定可以創建的超出 期望 Pod 個數的 Pod 數量。此值可以是絕對數（例如，5）或所需 Pods 的百分比（例如，10%）。 如果 MaxUnavailable 為 0，則此值不能為 0。百分比值會通過向上取整轉換為絕對數。 此字段的默認值為 25%。
> 
> 例如，當此值為 30% 時，啟動滾動更新後，會立即對新的 ReplicaSet 擴容，同時保證新舊 Pod 的總數不超過所需 Pod 總數的 130%。一旦舊 Pods 被殺死，新的 ReplicaSet 可以進一步擴容， 同時確保更新期間的任何時候運行中的 Pods 總數最多為所需 Pods 總數的 130%。
> 進度期限秒數
> 
> .spec.progressDeadlineSeconds 是一個可選字段，用於指定系統在報告 Deployment 進展失敗 之前等待 Deployment 取得進展的秒數。 這類報告會在資源狀態中體現為 Type=Progressing、Status=False、 Reason=ProgressDeadlineExceeded。Deployment 控制器將持續重試 Deployment。 將來，一旦實現了自動回滾，Deployment 控制器將在探測到這樣的條件時立即回滾 Deployment。
> 
> 如果指定，則此字段值需要大於 .spec.minReadySeconds 取值。
> 最短就緒時間
> .spec.minReadySeconds 是一個可選字段，用於指定新創建的 Pod 在沒有任意容器崩潰情況下的最小就緒時間， 只有超出這個時間 Pod 才被視為可用。默認值為 0（Pod 在準備就緒後立即將被視為可用）。 要瞭解何時 Pod 被視為就緒，可參考容器探針。

我看到最詳細簡單說明

>    minReadySeconds:
>        容器內應用程式的啟動時間，Kubernetes 會等待設定的時間後才繼續進行升級流程
>        如果沒有此欄位的話，Kubernetes 會假設該容器一開完後即可進行服務
>        若未設定此欄位，在某些極端情況下可能會造成服務無法正常運作(新誕生的 pod 尚未進入可服務階段)
>    maxSurge:
>        升級過程中最多可以比原先設定所多出的 pod 數量
>        此欄位可以為固定值或是比例(%)
>        ex. maxSurge: 1、replicas: 5，代表 Kubernetes 會先開好 1 個新 pod 後才刪掉一個舊的 pod，整個升級過程中最多會有 5+1 個 pod
>    maxUnavailable:
>        最多可以有幾個 pod 處在無法服務的狀態
>        當 maxSurge 不為零時，此欄位亦不可為零
>        ex. maxUnavailable: 1，代表 Kubernetes 整個升級過程中最多會有 1 個 pod 處在無法服務的狀態

參考:[透過 Kubernetes Deployments 實現滾動升級 | Kubernetes](https://tachingchen.com/tw/blog/kubernetes-rolling-update-with-deployment/)



[[Kubernetes] Deployment Overview | 小信豬的原始部落](https://godleon.github.io/blog/Kubernetes/k8s-Deployment-Overview/)

## rolling update 更新順序圖(重要)

怕之後忘記，特別紀錄一下更新過程。

1. 最原始 Deployment 狀況

{% mermaid TD %}
    A[Deparment] -->B(ReplicaSet 1)
    B --> D[Pod 1]
    B --> E[Pod 2]
    B --> F[Pod 3]
{% endmermaid %}

2. 佈署新版本，會新增 ReplicaSet 會如下

{% mermaid TD %}
    A[Deparment] -->B(ReplicaSet 1)
    A -->B2(ReplicaSet 2)
    B --> D[Pod 1]
    B --> E[Pod 2]
    B --> F[Pod 3]
{% endmermaid %}

3. 剛加的 ReplicaSet 會把Pod先建立，成功的話，才會把ReplicaSet 舊的刪掉，查看下面兩個圖比較清楚。

{% mermaid TD %}
    A[Deparment] -->B|Repica: 3|(ReplicaSet 1)
    A -->|Repica: 1 |B2(ReplicaSet 2)
    B --> D[Pod 1]
    B --> E[Pod 2]
    B --> F[Pod 3]
    B2 -->| 先建立一個pod| G[Pod 4]
{% endmermaid %}

{% mermaid TD %}
    A[Deparment] -->|Repica: 2|B(ReplicaSet 1)
    A -->|Repica: 1 |B2(ReplicaSet 2)
    B .->|刪除一個Pod| D[Pod 1]
    B --> E[Pod 2]
    B --> F[Pod 3]
    B2 -->G[Pod 4]
{% endmermaid %}


4. 剛加的 ReplicaSet 會把Pod先建立，成功的話，才會把ReplicaSet 舊的刪掉(loop第二次)


{% mermaid TD %}
    A[Deparment] -->|Repica: 2|B(ReplicaSet 1)
    A -->|Repica: 2 |B2(ReplicaSet 2)
    B --> E[Pod 2]
    B --> F[Pod 3]
    B2 -->G[Pod 4]
    B2 -->|建立一個Pod|H[Pod 5]
{% endmermaid %}

{% mermaid TD %}
    A[Deparment] -->|Repica: 1|B(ReplicaSet 1)
    A -->|Repica: 2 |B2(ReplicaSet 2)
    B .->|刪除一個Pod| E[Pod 2]
    B --> F[Pod 3]
    B2 -->G[Pod 4]
    B2 -->H[Pod 5]
{% endmermaid %}

5. 剛加的 ReplicaSet 會把Pod先建立，成功的話，才會把ReplicaSet 舊的刪掉(loop第三次)


{% mermaid TD %}
    A[Deparment] -->|Repica: 1|B(ReplicaSet 1)
    A -->|Repica: 3 |B2(ReplicaSet 2)
    B --> F[Pod 3]
    B2 -->G[Pod 4]
    B2 -->H[Pod 5]
    B2 -->|建立新的Pod|J[Pod 6]
{% endmermaid %}

{% mermaid TD %}
    A[Deparment] -->|Repica: 0|B(ReplicaSet 1)
    A -->|Repica: 3 |B2(ReplicaSet 2)
    B .-> |刪除一個Pod|F[Pod 3]
    B2 -->G[Pod 4]
    B2 -->H[Pod 5]
    B2 -->J[Pod 6]
{% endmermaid %}

{% mermaid TD %}
    A[Deparment] -->|Repica: 0|B(ReplicaSet 1)
    A -->|Repica: 3 |B2(ReplicaSet 2)
    B .-> |刪除一個Pod|F[Pod 3]
    B2 -->G[Pod 4]
    B2 -->H[Pod 5]
    B2 -->J[Pod 6]
{% endmermaid %}

**這邊要注意**舊的ReplicaSet不會刪除掉，因為之後RollBack程式透過上面機制可以做相反。

## 刪除 Deployment

```bash
kubectl delete deployments kuard-deployment

```


所有的 Pod ,ReplicaSet 都會一起刪除


## 感想

上面我測試 Service 更做更新的時候
不是會有舊新同時運行狀況
後面書上有提到前端到後端，前端要做好版本前後相容問題
這樣在切換上才不會發生問題


## 小記

Deployment 就是用 ReplicaSet 進行的 Pod 做版本更新(升級、降級)。
一個 Deployment ->最新兩個ReplicasSet ->Pod，做 Rolling  Update 這個流程很重要，可以看上面更新流程圖

