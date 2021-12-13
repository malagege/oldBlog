---
title: Kubernetes 的 Job
date: 2021-02-22 19:08:59
tags: [kubernetes, job]
categories: Kubernetes
---

直接進入重點

<!--more-->

## Job

會結束的程式。不會結束的會稱為Daemon。

## 執行方式

### Completions/Parallelism 

Completions: 啟動任務要執行幾次
Parallelism: 同時間執行幾個程式


#### Job Completions: 2 , Parallelism: 1

{% mermaid graph %}
    A[Job Completions: 2 , Parallelism: 1] -->|Step.1| B[建立 Pod]
    B --> |Step.2| C[Pod執行中]
    C --> |Step.3| D[Pod 執行完成]
    D -->|Step.4 Observe| A
    A --> |Step.5| B1[建立 Pod]
    B1 --> |Step.6|C1[Pod執行中]
    C1 --> |Step.7|D1[Pod 執行完成]
    D1 -->|Step.8  Observe| A
{% endmermaid %}

#### Job Completions: 2 , Parallelism: 2

用程式觀點，簡單說就是2個`執行緒`執行`，同時間執行兩個工作。

{% mermaid graph %}
    A[Job Completions: 2 , Parallelism: 2] -->|Step1.1| B[建立 Pod]
    B --> |Step-1.2| C[Pod執行中]
    C --> |Step-2.3| D[Pod 執行完成]
    D -->|Step-1.4 Observe| A
    A --> |Step-2.1| B1[建立 Pod]
    B1 --> |Step-2.2|C1[Pod執行中]
    C1 --> |Step-2.3|D1[Pod 執行完成]
    D1 -->|Step-2.4  Observe| A 
{% endmermaid %}

上面圖片Step1,Step2 是一起做的。我多加一個Thread 來表示可能會比較清楚。


{% mermaid graph %}
    ST[StartJob]  --> |Step-1 and 2|B & B1
    subgraph thread1
    B[建立 Pod] --> |Step-1.2| C[Pod執行中]
    C --> |Step-1.3| D[Pod 執行完成]
    end
    subgraph thread2
    B1[建立 Pod] --> |Step-2.2|C1[Pod執行中]
    C1 --> |Step-2.3|D1[Pod 執行完成]
    end
    D & D1 --> END[EndJob]
{% endmermaid %}

### backofflimit

跑失敗幾次就不跑

### activeDeadLineSeconds

執行超過多久會自動關閉，Pod狀態會變成failed。

### Tree

Job->Pod*
## 官方實例

```yml job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```


```bash
kubectl apply -f https://kubernetes.io/examples/controllers/job.yaml
```


## 自動清理

完成 100 秒就會清除，我在 kind 沒有效果...

> FEATURE STATE: Kubernetes v1.12 [alpha]

可能我的環境還不支援??

```bash
apiVersion: batch/v1
kind: Job
metadata:
  name: pi-with-ttl
spec:
  ttlSecondsAfterFinished: 100
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
```

## Crontab 

```yml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            imagePullPolicy: IfNotPresent
            args:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure

```


## Queue Work

- [使用工作队列进行粗粒度并行处理 | Kubernetes](https://kubernetes.io/zh/docs/tasks/job/coarse-parallel-processing-work-queue/)
- [详解 Kubernetes Job 和 CronJob 的实现原理 - 面向信仰编程](https://draveness.me/kubernetes-job-cronjob/)

## Tree
```
kubectl tree cronjob xxx
```

CronJob->Job->Pod




