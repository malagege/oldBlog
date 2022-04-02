---
title: Kubernetes 的 ConfigMap
date: 2021-03-03 20:54:32
tags: [configmap]
categories: Kuberenetes
---

Kubernetes 的 ConfigMap 常常會跟 Storage 有關係。
 

可以針對

- volume(檔案設定檔)
- environment variable (環境變數)

<!--more-->

## 設定關鍵字

掛載檔案方式:
- 使用 `Volume` 掛在 Pod
- 使用 `VolumeMount` 掛在 Container

套用環境變數上:
- 使用 `configMapKeyRef` 掛在 環境變數

## 建立 Clusters

```bash
kubectl create configmap <map-name> <data-source>
```

```bash
# Create the local directory
mkdir -p configure-pod-container/configmap/

# Download the sample files into `configure-pod-container/configmap/` directory
wget https://kubernetes.io/examples/configmap/game.properties -O configure-pod-container/configmap/game.properties
wget https://kubernetes.io/examples/configmap/ui.properties -O configure-pod-container/configmap/ui.properties

# Create the configmap
kubectl create configmap game-config --from-file=configure-pod-container/configmap/
```

這邊有點有趣，client 端做，nodes 竟然可以吃到抓下來的 properties
不知道底層原理怎麼做到

```bash
kubectl get configmaps game-config -o yaml
```


```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  creationTimestamp: 2016-02-18T18:52:05Z
  name: game-config
  namespace: default
  resourceVersion: "516"
  uid: b4952dc3-d670-11e5-8cd0-68f728db1985
data:
  game.properties: |
    enemies=aliens
    lives=3
    enemies.cheat=true
    enemies.cheat.level=noGoodRotten
    secret.code.passphrase=UUDDLRLRBABAS
    secret.code.allowed=true
    secret.code.lives=30    
  ui.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
    how.nice.to.look=fairlyNice    
```


### 從檔案建立 ConfigMap

```bash
kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/game.properties
```

```bash
kubectl describe configmaps game-config-2
```

```
Name:         game-config-2
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
game.properties:
----
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30
Events:  <none>
```

```bash
# 同時使用--from-file 載入多個檔案
kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/game.properties --from-file=configure-pod-container/configmap/ui.properties

# 查看 ConfigMap 內容
kubectl describe configmaps game-config-2
```


```
Name:         game-config-2
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
game.properties:
----
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30
ui.properties:
----
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice
```


```bash
#Download the sample files into `configure-pod-container/configmap/` directory
wget https://kubernetes.io/examples/configmap/game-env-file.properties -O configure-pod-container/configmap/game-env-file.properties

# The env-file `game-env-file.properties` looks like below
cat configure-pod-container/configmap/game-env-file.properties
enemies=aliens
lives=3
allowed="true"

# This comment and the empty line above it are ignored


kubectl get configmap game-config-env-file -o yaml
# apiVersion: v1
# kind: ConfigMap
# metadata:
#   creationTimestamp: 2017-12-27T18:36:28Z
#   name: game-config-env-file
#   namespace: default
#   resourceVersion: "809965"
#   uid: d9d1ca5b-eb34-11e7-887b-42010a8002b8
# data:
#   allowed: '"true"'
#   enemies: aliens
#   lives: "3"
```


使用多個`--from-env-file` 

```bash
# Download the sample files into `configure-pod-container/configmap/` directory
wget https://kubernetes.io/examples/configmap/ui-env-file.properties -O configure-pod-container/configmap/ui-env-file.properties

# Create the configmap
kubectl create configmap config-multi-env-files \
        --from-env-file=configure-pod-container/configmap/game-env-file.properties \
        --from-env-file=configure-pod-container/configmap/ui-env-file.properties

```

```bash
kubectl get configmap config-multi-env-files -o yaml
# apiVersion: v1
# kind: ConfigMap
# metadata:
#   creationTimestamp: 2017-12-27T18:38:34Z
#   name: config-multi-env-files
#   namespace: default
#   resourceVersion: "810136"
#   uid: 252c4572-eb35-11e7-887b-42010a8002b8
# data:
#   color: purple
#   how: fairlyNice
#   textmode: "true"

```


```bash
#kubectl create configmap game-config-3 --from-file=<my-key-name>=<path-to-file>
kubectl create configmap game-config-3 --from-file=game-special-key=configure-pod-container/configmap/game.properties

kubectl get configmaps game-config-3 -o yaml

# apiVersion: v1
# kind: ConfigMap
# metadata:
#   creationTimestamp: 2016-02-18T18:54:22Z
#   name: game-config-3
#   namespace: default
#   resourceVersion: "530"
#   uid: 05f8da22-d671-11e5-8cd0-68f728db1985
# data:
#   game-special-key: |
#     enemies=aliens
#     lives=3
#     enemies.cheat=true
#     enemies.cheat.level=noGoodRotten
#     secret.code.passphrase=UUDDLRLRBABAS
#     secret.code.allowed=true
#     secret.code.lives=30    
```



指令內文 key

```bash
kubectl create configmap special-config --from-literal=special.how=very --from-literal=special.type=charm
kubectl get configmaps special-config -o yaml
# apiVersion: v1
# kind: ConfigMap
# metadata:
#   creationTimestamp: 2016-02-18T19:14:38Z
#   name: special-config
#   namespace: default
#   resourceVersion: "651"
#   uid: dadce046-d673-11e5-8cd0-68f728db1985
# data:
#   special.how: very
#   special.type: charm
```



```bash
# Create a kustomization.yaml file with ConfigMapGenerator
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: game-config-4
  files:
  - configure-pod-container/configmap/game.properties
EOF


kubectl apply -k .



kubectl describe configmaps/game-config-4-m9dm2f92bt
```

定義 key Mapping 到 檔案

```bash
# Create a kustomization.yaml file with ConfigMapGenerator
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: game-config-5
  files:
  - game-special-key=configure-pod-container/configmap/game.properties
EOF


kubectl apply -k .
```


```
# Create a kustomization.yaml file with ConfigMapGenerator
cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: special-config-2
  literals:
  - special.how=very
  - special.type=charm
EOF


kubectl apply -k .
```


## 在 Pod 使用 ConfigMap

### 使用環境變數

```bash
kubectl create configmap special-config --from-literal=special.how=very

```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
      env:
        # Define the environment variable
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              # The ConfigMap containing the value you want to assign to SPECIAL_LEVEL_KEY
              name: special-config
              # Specify the key associated with the value
              key: special.how
  restartPolicy: Never
```


```bash
kubectl create -f https://kubernetes.io/examples/pods/pod-single-configmap-env-variable.yaml
```


使用多個 ConfigMap

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  special.how: very
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: env-config
  namespace: default
data:
  log_level: INFO

```


```bash
kubectl create -f https://kubernetes.io/examples/configmap/configmaps.yaml
```


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
      env:
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: special.how
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: env-config
              key: log_level
  restartPolicy: Never

```


```bash
kubectl create -f https://kubernetes.io/examples/pods/pod-multiple-configmap-env-variable.yaml
```


TODO: https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/#define-container-environment-variables-with-data-from-multiple-configmaps


## cheatsheet


```bash
kubectl apply -R -f intrudction/configmap
kubectl get configmap
kubectl get pods
kubectl exec pods
ls /tmp/config
env
```


## 同步時間

apiserver -> kubelet -> volume
一個箭頭 大約一分鐘

總共加起來需要兩分鐘。


## env 安全性問題

取得 node 節點可以看到 container 環境設定變數問題