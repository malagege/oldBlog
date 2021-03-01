---
title: Kubernetes 的 Job
date: 2021-02-22 19:08:59
tags: [kubernetes, job]
categories: Kubernetes
---

直接進入重點

<!--more-->

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