---
title: Kubernetes 的 Label 和 Annotation 小記
date: 2021-02-02 22:07:39
tags: [Kubernetes,label,annotation]
categories: Kubernetes
---

Kubernetes 的 Label 和 Annotation 小記

<!--more-->

## Label

### 實作

```bash
kubectl run alpaca-prod \
--image=gcr.io/kuar-demo/kuard-amd64:blue \
--replicas=2 \ # 這個已經廢棄
--labels="ver=1,app=alpaca,env=prod"
```

```bash
kubectl run alpaca-test \
--image=gcr.io/kuar-demo/kuard-amd64:green \
--replicas=1 \ # 這個已經廢棄
--labels="ver=2,app=alpaca,env=test"
```

```bash
kubectl run bandicoot-prod \
--image=gcr.io/kuar-demo/kuard-amd64:green \
--replicas=2 \ # 這個已經廢棄
--labels="ver=2,app=bandicoot,env=prod"
```

```bash
kubectl run bandicoot-staging \
--image=gcr.io/kuar-demo/kuard-amd64:green \
--replicas=1 \
--labels="ver=2,app=bandicoot,env=prod"
```


`Flag --replicas has been deprecated, has no effect and will be removed in the future.`
> 在K8S v1.18.0 以後，--replicas已棄用 ,推薦用 deployment 創建 pods
[解决k8s 1.18.0版本 replicas 被弃用问题 - 菜鸟bai - 博客园](https://www.cnblogs.com/cnbai/p/12911521.html)

```bash
 kubectl get pods --show-labels 
# NAME                READY   STATUS    RESTARTS   AGE     LABELS
# alaca-prod          1/1     Running   0          2m46s   app=alpaca,env=prod,ver=1
# alpaca-test         1/1     Running   0          2m11s   app=alpaca,env=test,ver=2
# bandicoot-prod      1/1     Running   0          2m4s    app=bandicoot,env=prod,ver=2
# bandicoot-staging   1/1     Running   0          117s    app=bandicoot,env=prod,ver=2 
```
 
> Remove all the generators from kubectl run. It will now only create pods.
[minikube - How to start a pod in command line without deployment in kubernetes? - Stack Overflow](https://stackoverflow.com/a/62036673)

這邊我書上都寫 deployments 想說怎麼跟我電腦跑的結果不一樣


```bash
kubectl label pods alpaca-test "canary=true"                                                                ✔  22  22:27:42 
# pod/alpaca-test labeled
kubectl get pods alpaca-test -L canary                                                                      ✔  23  22:28:03 
# NAME          READY   STATUS    RESTARTS   AGE   CANARY
# alpaca-test   1/1     Running   0          12m   true
kubectl label podsalpaca-test "canary-"
# pod/alpaca-test labeled
```

## Label 選擇器

```bash
kubectl get pods --show-labels
# NAME                READY   STATUS    RESTARTS   AGE   LABELS
# alaca-prod          1/1     Running   0          16m   app=alpaca,env=prod,ver=1
# alpaca-test         1/1     Running   0          15m   app=alpaca,env=test,ver=2
# bandicoot-prod      1/1     Running   0          15m   app=bandicoot,env=prod,ver=2
# bandicoot-staging   1/1     Running   0          15m   app=bandicoot,env=prod,ver=2
```

```bash
kubectl get pods --selector="ver=2"
# NAME                READY   STATUS    RESTARTS   AGE
# alpaca-test         1/1     Running   0          22m
# bandicoot-prod      1/1     Running   0          22m
# bandicoot-staging   1/1     Running   0          22m
kubectl get pods --selector="app=bandicoot,ver=2"
# NAME                READY   STATUS    RESTARTS   AGE
# bandicoot-prod      1/1     Running   0          23m
# bandicoot-staging   1/1     Running   0          23m
kubectl get pods --selector="app in (alpaca,bandicoot)"
# NAME                READY   STATUS    RESTARTS   AGE
# alpaca-prod         1/1     Running   0          4m35s
# alpaca-test         1/1     Running   0          26m
# bandicoot-prod      1/1     Running   0          26m
# bandicoot-staging   1/1     Running   0          26m
kubectl get pods --selector="canary"
# No resources found in default namespace.
kubectl get pods --selector='!canary'
# NAME                READY   STATUS    RESTARTS   AGE
# alpaca-prod         1/1     Running   0          7m19s
# alpaca-test         1/1     Running   0          29m
# bandicoot-prod      1/1     Running   0          29m
# bandicoot-staging   1/1     Running   0          29m
kubectl get pods -l 'ver=2,!canary'
# NAME                READY   STATUS    RESTARTS   AGE
# alpaca-test         1/1     Running   0          30m
# bandicoot-prod      1/1     Running   0          30m
# bandicoot-staging   1/1     Running   0          30m
```


### Yaml 寫 IN 方法

kubectl get pods -l 'environment in (production, qa)'

```yaml
selector:
  matchLabels:
    component: redis
  matchExpressions:
    - {key: tier, operator: In, values: [cache]}
    - {key: environment, operator: NotIn, values: [dev]}
```

可參考:[Labels and Selectors | Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)

舊版寫法(因為相容還是可以跑)

```yml
selector:
  app: alpaca
  ver: 1
```

## Annotation

我看書沒特別介紹
感覺他就是Java HashMap 可以任意存任何key value 值

```yaml
...
metadata:
  anotations:
    xxxx.com/url: "http://xxxx"
```

## 移除範例 pods

```
kubectl delete pods --all
```

delete 也可以搭配篩選器`selector`