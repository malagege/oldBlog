---
title: 在 Kubernetes 用 Loki 蒐集 log 做監控警告通知方法整理筆記
date: 2022-04-03 00:23:52
tags: [Kubernetes,loki]
categories: Kubernetes 
---

## 前言

很高興這次有這個機會接觸雲端服務，我目前職位程式工程師，最近也對雲端技術也非常有興趣，所以自學Docker、Kubernetes(還在自學中）。不過我目前沒有實務上經驗，在下面這幾篇會以我成功實作紀錄，所以我的筆記理論可能會有誤，也歡迎~~手下留情~~大家指正。
廢話不多說，直接 show 出我這次研究筆記，我把廢話放在感言。


<!--more-->

## 筆記

### 連線主機前置動作

- [sshconfig 設定小記](https://malagege.github.io/blog/2022/02/13/sshconfig-%E8%A8%AD%E5%AE%9A%E5%B0%8F%E8%A8%98/)
- [使用 Bastion Host 方式ssh方式登入主機](https://malagege.github.io/blog/2022/02/13/%E4%BD%BF%E7%94%A8-Bastion-Host-%E6%96%B9%E5%BC%8Fssh%E6%96%B9%E5%BC%8F%E7%99%BB%E5%85%A5%E4%B8%BB%E6%A9%9F/)

### Kubernetes、Traefik、監控等安裝筆記

- [雲端虛擬機 Ubuntu 20.04 安裝 Kubernetes](https://malagege.github.io/blog/2022/04/02/%E9%9B%B2%E7%AB%AF%E8%99%9B%E6%93%AC%E6%A9%9F-Ubuntu-20-04-%E5%AE%89%E8%A3%9D-Kubernetes/)
- [使用 Helm 快速搭建 Traefik Ingress Controller](https://malagege.github.io/blog/2022/04/03/%E4%BD%BF%E7%94%A8-helm-%E5%BF%AB%E9%80%9F%E6%90%AD%E5%BB%BA-Traefik-Ingress-Controller/)
- [使用 Helm 快速安裝 Loki](https://malagege.github.io/blog/2022/04/03/%E4%BD%BF%E7%94%A8-Helm-%E5%BF%AB%E9%80%9F%E5%AE%89%E8%A3%9D-Loki/)
- [Loki 做監控動作方法](https://malagege.github.io/blog/2022/04/03/Loki-%E5%81%9A%E7%9B%A3%E6%8E%A7%E5%8B%95%E4%BD%9C%E6%96%B9%E6%B3%95/)

## 感言

第一次接觸 Web 管理虛擬主機不是很習慣，我本來就像 Virtalbox 那樣建立機器就可以了，但看到網路有要設定路由器，剛看介面完全不知道要怎麼使用，看文件有些細節也不知道要如何使用，如用路由器綁定浮動 IP 。最後有請教我朋友做網路工程師，他有看文件大概跟我講一些設定，但他說有些設定他也不是很懂，如找不到Portfording 設定...，總之我順利開啟機器。

![圖片](https://user-images.githubusercontent.com/6058558/161392241-c4349952-624c-41bd-bedd-4e970d1c6e46.png)
![圖片](https://user-images.githubusercontent.com/6058558/161392274-a22f0a6e-75f8-4394-a352-afba818aba00.png)

設定IP也滿有趣的，以前 ifconfig 設定主機IP，這個系統可以直接設定主機IP安裝就會自動配置，後來我發現設兩個IP就不能了，後來發現主機有配置第一個 interface 做 DHCP，所以才會吃到 IP 設定，所以機器添加第二個就不會自動設定，最新版換 IP 配置用 yaml，對於遠端連線設定 IP 也是非常方便，就算設錯超過確認時間，也會幫你設定回去，可參考:[Ubuntu 18.04 透過 netplan 設定網路卡 IP - Soul & Shell Blog](https://blog.toright.com/posts/6293/ubuntu-18-04-%E9%80%8F%E9%81%8E-netplan-%E8%A8%AD%E5%AE%9A%E7%B6%B2%E8%B7%AF%E5%8D%A1-ip.html)。

首先查看[CNTUG Infra Labs OpenStack 使用教學](https://github.com/cloud-native-taiwan/Infra-Labs-Docs/blob/main/user-guide/openstack.md)文件，看到文件有建議使用 bastion host 。

> OpenStack 允許各個用戶在自己專案建立自己的私有網路，私有網路中的 VM 可以透過路由器 SNAT 連線至外部網路。 我們會建議使用者建立一個自己的私有網路並且透過一個 bastion host 作為一個跳板連入。

這種登入方式也滿有趣，也整理滿多東西，不知道還有 ProxyJump 可以使用，之前公司環境不可以直接連資料庫，後來發現 SSH Tunnel 可以使用，可參考[這篇](https://malagege.github.io/blog/2018/12/13/ssh-port-forward%E9%80%A3MySQL%E6%96%B9%E6%B3%95/)，也可以直接用工具連。我以前公司內部有幾個主機也需要跳板進去，我們都 ssh 跳板主機然後再 ssh 別台主機，這種麻煩的方式，這次能發現使用 `sshconfig`和 `proxyjump` 可以解決我這個問題。甚至 scp 我也不用在兩台主機打兩次指令，直接透過`sshconfig`和 `proxyjump` 做到傳檔案，可以參考[這篇](https://nyogjtrc.github.io/posts/2020/03/ssh-proxyjump/)。

Kubernetes 首次安裝也採到一些雷，我以為 `kubectl apply`就可以安裝 CNI，不過使用 calic 最後 Service 都連不到，目前猜測我網域 IP 跟內部衝突導致的?不過我現在用 Flannel 可正常使用。很多教學使用 Nginx 當作 Ingress Controller ，我家動物機使用 Traefik 當作接口，想說這次使用看看，當然也有碰到一些雷，使用 Traefik Dashboard 照上面操作還是會 404，就算 SSH Tunnel 也會出現 404，不知道 bastion host 做 SSH Tunnel 會不會有影響？網路上有一些文章有寫教學，結果操作發現不能運作，好險對照 Helm Loki/Prometheus Chart 裡面設定檔有~~對照~~猜出來，最後很高興有實作出來。
