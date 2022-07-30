---
title: Kubernetes Storage小記
date: 2022-07-30 23:13:06
tags: [kubernetes,storage]
categories: Kubernetes
---

小記相關東西。

<!--more-->

## HostPath

一個 Kubernetes 內建功能。把節點(node)把路徑分享到Pod。
但這個方法無法多節點(Node)共享檔案。


## Storage Read/Write

讀跟寫機制:
- Read Only Many
同時很多人讀不能寫
- Read Write Once
同時只能一個用，他可以讀可以寫
- Read Write Many
他可以很多人使用，可同時讀寫。

NFS 是`Read Write Many`



## 安全機制

PodSecurityPolicy

## docker 的 volumes


各節點都可以取得 volumes 問題


1. volume 
2. container 加入 volumeMounts 設定


## 私有雲

azureDisk
awsElasticBlockStore
gcePersistentDisk

GephFS
Glusters
iscsi
nfs


Volume Type:

- CSI
- downwardAPI
- emptyDir
- hostPath
- configMap
- Secret
Flexible management
- persistentVolumeClaim


## persistentVolume 好處

一般掛載 Volume 掛載NFS ，修改Volume，能不能讓所有Pod不需要重啟，透過PV抽象出來。

有兩種可以使用
- PV
- PVC

在 POD 裡面我們使用PVC，儲存設備有什麼資源、儲存大小、儲存模式(Readwrite Readonly One/Many)，以Clinet端角度去思考。PVC會找符合PV條件使用。 

PV 沒 namespace，PVC有 namespace。

## PVC 移掉


- Retain

- Delete

- Recelile

## bind 問題

無法選最優的選擇。

可以強制使用哪一個PV。

1. 安裝NFS
2. PV去使用NFS
3. PVC去綁PV
4. Pod 曲榜 PVC


取決StorageProvider 是否有支援PVC/PV。
如NFS沒有Capacity 概念，不會管硬碟空間多大。



## CSI




## PV/PVC

PV,PVC都是靜態部屬(static Provisioning)。管理不需要手動建立PV，而根據PVC需求自動建置。



## StorageClass

NFS StorageClass Provisioner

NFS Server 不是 Provisioner。