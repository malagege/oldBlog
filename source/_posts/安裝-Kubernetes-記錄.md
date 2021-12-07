---
title: 安裝 Minikube 記錄
date: 2021-01-14 22:53:44
tags: [kubernetes]
categories: Kubernetes
---


## minkube

### 安裝 minikube

必備安裝
- VirtualBox
- minikube
- kubectl

#### Minikube


[(這邊請去官網看OS載點替換) kubernetes/minikube](https://github.com/kubernetes/minikube/releases)

```sh
curl -LO https://github.com/kubernetes/minikube/releases/download/v1.6.2/minikube-linux-amd64(這邊請去官網看OS載點替換)
chmod +x minikube-linux-amd64
./minikube-linux-amd64 # 測試看能不跑
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
```


#### Virtualbox 

[Linux_Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads)

我這邊是用 ElementaryOS (Ubuntu 18.04)
下載完成用 `sudo apt install ./xxx.deb` 指令安裝

#### 安裝  kubectl

單節點

[ (這邊請去官網看OS載點替換) Install and Set Up kubectl - Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

這邊我選 Linux
```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl (這邊請去官網看OS載點替換)
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl # 看能不能跑
```


#### minikube start

```sh
minikube start
# 會安裝會用到的 iso
😄  minikube v1.6.2 on Debian buster/sid
✨  Automatically selected the 'virtualbox' driver (alternates: [none])
🔥  Creating virtualbox VM (CPUs=2, Memory=2000MB, Disk=20000MB) ...
🐳  Preparing Kubernetes v1.17.0 on Docker '19.03.5' ...
🚜  Pulling images ...
🚀  Launching Kubernetes ... 
⌛  Waiting for cluster to come online ...
🏄  Done! kubectl is now configured to use "minikube"
 ```

最後可以下`minikube dashboard`
看有沒有問題

##### minikube start 選項功能(沒用 vagrant 可以跳過)

```bash
--vm-drivers(default 是Virtualbox)
none 是用本機

--Bootstrapper(預設使用kubeadm)
```

在 vagrant 安裝 minikube 可以做`sudo minikube start --vm-drivers=none`

### 遇到錯誤

#### VM 64 bit 不能安裝 64 bit OS

**先說結論:不能用 VM 跑樣子**

用 VM 裝可能遇到的問題
```
💣  Unable to start VM. Please investigate and run 'minikube delete' if possible
❌  Error: [VBOX_VTX_DISABLED] create: precreate: This computer doesn't have VT-X/AMD-v enabled. Enabling it in the BIOS is mandatory
```

```
config.vm.provider :virtualbox do |vb|  
  vb.customize ["modifyvm", :id, "--hwvirtex", "off"]     
end
```
參考來源: [Disable VT-X in Vagrantfile](https://gist.github.com/betweenbrain/7798873)
但最後還是不行

> Consequences from missing VT-X in nested VirtualBox
> 
> There are currently two consequences from nesting without VT-X:
> 
>     You cannot use multiple CPUs but you can use a single virtual cpu using vb.cpus = 1
>     You cannot run a 64-Bit VM within a nested VirtualBox.

[Error VT-x not available for Vagrant machine inside Virtualbox - Stack Overflow](https://stackoverflow.com/questions/24620599/error-vt-x-not-available-for-vagrant-machine-inside-virtualbox)


#### Exec format error

若有這個錯誤訊息...

> bash: /usr/local/bin/kubectl: cannot execute binary file: Exec format error

就是安裝錯版本


### 修改權限

```bash
sudo chown `id -u`:`id -u` -R .minikube/
```

### 使用 minikube 的 kubectl

```bash
sudo minikube kubectl 


minikube kubectl --  -n kube-system get pods
```

### 查看 cluster-info 資訊

```
sudo minIkube kubectl -- cluster-info
```
或
```
kubectl cluster-info
Kubernetes control plane is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

成功完成

用 vagrant 安裝可能會遇到權限問題可以使用問題，如下圖。
![圖片](https://user-images.githubusercontent.com/6058558/143443354-9a49f358-82a4-484b-9845-1ad8c5bbed09.png)
加上`sudo`可以解決。

![圖片](https://user-images.githubusercontent.com/6058558/143443576-49415c78-41ec-446c-b4b7-f9fcab390aec.png)




#### 權限問題(使用vagrant)

![圖片](https://user-images.githubusercontent.com/6058558/143443902-0cc3e0ae-0a4c-4c3e-bbcb-95c66dfc202e.png)

因為用 `sudo`安裝關係，設定檔有些是 root，照`minikube start`跑完上面設定有叫你下那些指令後，就可以正常使用。

```bash
#主要是第二段
sudo mv /home/vagrant/.kube /home/vagrant/.minikube $HOME
sudo chown -R $USER $HOME/.kube $HOME/.minikube
```


### 查看 minikube 的原件

![圖片](https://user-images.githubusercontent.com/6058558/143445101-2bf56d11-1c6b-4291-884d-15f06d9f7a91.png)

這邊可以看到他跟預設 k8s 安裝原件有差異，所以滿多人不建義用這個玩k8s。
多了Storage Provisioner，少了Flannel CNI。

### 刪除 minikube

Delete minikube

minikube delete && rm -rf ~/.minikube

### 遠端連 dashboard

#### 安裝 UI

以下參考: 
[kubernetes/dashboard: General-purpose web UI for Kubernetes clusters](https://github.com/kubernetes/dashboard)
[[Day 14] Kubernetes Dashboard 介紹 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10195385)


```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.1.0/aio/deploy/recommended.yaml

namespace/kubernetes-dashboard configured
serviceaccount/kubernetes-dashboard configured
service/kubernetes-dashboard configured
secret/kubernetes-dashboard-certs configured
secret/kubernetes-dashboard-csrf configured
secret/kubernetes-dashboard-key-holder configured
configmap/kubernetes-dashboard-settings configured
role.rbac.authorization.k8s.io/kubernetes-dashboard configured
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard configured
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard configured
deployment.apps/kubernetes-dashboard configured
service/dashboard-metrics-scraper configured
deployment.apps/dashboard-metrics-scraper configured
# 會遇到這個錯誤
The ClusterRoleBinding "kubernetes-dashboard" is invalid: roleRef: Invalid value: rbac.RoleRef{APIGroup:"rbac.authorization.k8s.io", Kind:"ClusterRole", Name:"kubernetes-dashboard"}: cannot change roleRef
```

這個錯誤，我目前無解...
因為刪除 kubernetes-dashboard 後會馬上跳出來

```sh
$ kubectl proxy
# 預設 localhost
# Starting to serve on 127.0.0.1:8001
kubectl proxy --address='0.0.0.0' --port=8002 --accept-hosts='^*$'
# 開啟遠端連線
```

其他資源:[kubernetes-dashboard(1.8.3)部署与踩坑 - 雨夜朦胧 - 博客园](https://www.cnblogs.com/rainingnight/p/deploying-k8s-dashboard-ui.html)

### 簡單 Hello World

[Hello Minikube | Kubernetes](https://kubernetes.io/docs/tutorials/hello-minikube/)

## Kind 

### 安裝 Docker

```
curl -sSL https://get.daocloud.io/docker | sh
```
詳細建議官網看新教學


#### 設定 docker 權限

```bash
sudo usermod -G docker -a testuser
```

做完重新登錄

### 安裝 Kind

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.9.0/kind-linux-amd64
chmod +x ./kind
mv ./kind /usr/local/bin/kind
```

### 啟用集群

```
kind create cluster
```

### 刪除集群

```
kind delete cluster
```

這邊要注意一點
移除集群預設選擇設定會拿掉
所以要重新選擇 k8s config context

```sh
# 查看現有選擇 k8s 環境
kubectl config current-context
# 列出所有環境
kubectl config get-contexts
# 選擇 k8s 環境
kubectl config use-context minikube
```
更多詳細設定請參考[Day 8 - 與 k8s 溝通: kubectl - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10193502)
外來可能會針對設定檔做設定

這樣兩個環境就建置出來

相關教學:
[kind – Quick Start](https://kind.sigs.k8s.io/docs/user/quick-start) 

[使用 kind 快速搭建 kubernetes 环境 - WeihanLi - 博客园](https://www.cnblogs.com/weihanli/p/12831225.html)
[使用 Kind 在 5 分钟内快速部署一个 Kubernetes 高可用集群 - 奇妙的 Linux 世界](https://www.hi-linux.com/posts/42332.html)
[快速搭建kubernetes与kubeSphere环境（亲测有效）](https://juejin.cn/post/6844903991499833358)

[【容器架构】Minikube vs.kind vs.k3s-我应该用哪一个？ | 架构师智库](http://jiagoushi.pro/minikube-vs-kind-vs-k3s-what-should-i-use)

[不同Kubernetes构建工具的介绍 - 知乎](https://zhuanlan.zhihu.com/p/117558052)

[使用 WSL 2 與 Docker Desktop 架設 Kubernetes 多節點叢集環境 (KinD) | The Will Will Web](https://blog.miniasp.com/post/2020/08/21/Install-Kubernetes-cluster-in-WSL-2-Docker-on-Windows-using-kind)


### 查看 minikube plugin

```
sudo minikube addons list
```

![圖片](https://user-images.githubusercontent.com/6058558/143445533-37c3308b-dfa2-43d8-9a0d-46ec1af5c67d.png)

可以快速安裝 k8s 原件。

```
sudo minikube addons enable dashboard
```

![圖片](https://user-images.githubusercontent.com/6058558/143446012-17d4491c-36d1-440c-b666-a4c7ebbffcdc.png)


上圖可以看到他建立新的容器。對外測試連結容器可以用
```
kubectl port-forward --address  _IPADDRESS_-n kubernetes-dashboard service/kubernetes-dashboard 8888:80
```