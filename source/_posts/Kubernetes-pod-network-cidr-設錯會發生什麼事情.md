---
title: Kubernetes pod-network-cidr 設錯會發生什麼事情
date: 2022-04-02 23:51:43
tags: [kubernetes]
categories: Kubernetes
---


最近看到[Install Kubernetes Cluster on Ubuntu 20.04 with kubeadm | ComputingForGeeks](https://computingforgeeks.com/deploy-kubernetes-cluster-on-ubuntu-with-kubeadm/)教學，裡面提到使用 IP `192.168.0.0/16`，因為我區網範圍在 `192.168.1.0/24`，原本照常安裝 Kubernetes，但是 Kubernetes Worker 主機沒辦法連到 Kubernetes Master 主機，Pod那些都看起來正常。

tracepath 發現路徑會傳到外網網路。


<!--more-->

## pod-network-cidr 不是可以隨便設定的


後來發現文件有寫避免用相同 IP 範圍。我就調整 `pod-network-cidr`，IP範圍，但發現 Network 相關 Pod 都沒有啟動，經過多次測試、重置，調整原來的`pod-network-cidr` 就會產生 Network 的 Pod，我就發覺這個不是簡單可以調整，單然還是可以調整。


## 調整方式

要調整方式我稍微網路找一下方法，發現需要一些步驟，改天熟悉 Kubernetes 時候有需求在操作看看。

### Flannel

[Kubernetes 使用kubeadm创建集群 - SegmentFault 思否](https://segmentfault.com/a/1190000041057390)

## Calico

[calico修改podCIDR - 雷探长 - 博客园](https://www.cnblogs.com/ltzhang/p/14315683.html)


## 重置發生問題(彩蛋)

[Kubernetes(K8s) 使用kubeadm reset重置后 kubeadm init 失败的解决方法-CJavaPy](https://www.cjavapy.com/article/2392/)


[Calico配置及原理 | Mr.Ye Blogs](https://system51.github.io/2020/05/27/using-calico/)

[虚拟化运维--容器网络--K8S+Calico(十三) - 简书](https://www.jianshu.com/p/dbf4a343f56c)
[故障排查：Kubernetes 中 Pod 无法正常解析域名 | Linux经验之谈](https://www.gylinux.cn/4299.html)
[常見 CNI (Container Network Interface) Plugin 介紹 | Hwchiu Learning Note](https://www.hwchiu.com/cni-compare.html)
[簡單5步，輕鬆debug K8S服務！ | IT人](https://iter01.com/510519.html)


![](https://i.imgur.com/0DI9AYI.png)
