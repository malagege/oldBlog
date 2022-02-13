---
title: Vagrant + Virtualbox 建置 k8s 筆記
date: 2022-02-13 23:47:20
tags: [vagrant,virtualbox,kubernetes,vm]
categories: Kubernetes
---

Vagrant + Virtualbox 建置 k8s 筆記

<!--more-->
## Vagrant Build Image

```ruby=
Vagrant.configure("2") do |config|
  # 設定 vm image, 版本,hostname,名稱
  config.vm.box = "bento/ubuntu-18.04"
  config.vm.box_version ='201912.14.0'
  config.vm.hostname = 'k8s-dev'
  config.vm.define vm_name = 'k8s'

  # provision (Shell Script)
  config.vm.provision "shell", privileged: false, inline: <<-SHELL
    set -e -x -u
    export DEBIAN_FRONTEND=noninteractive

    #change the source.list
    sudo apt-get update
    sudo apt-get install -y vim git cmake build-essential tcpdump tig jq socat bash-completion
    # Install Docker
    export DOCKER_VERSION="5:19.03.5~3-0~ubuntu-bionic"
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    sudo apt-get update
    sudo apt-get install -y docker-ce=${DOCKER_VERSION}
    sudo usermod -aG docker $USER

    #Disable swap
    #https://github.com/kubernetes/kubernetes/issues/53533
    sudo swapoff -a && sudo sysctl -w vm.swappiness=0
    sudo sed '/vagrant--vg-swap/d' -i /etc/fstab


    git clone --depth=1 https://github.com/Bash-it/bash-it.git ~/.bash_it
    bash ~/.bash_it/install.sh -s

  SHELL

  # 設定IP
  config.vm.network :private_network, ip: "172.17.8.111"
  #設定硬體
  config.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ['modifyvm', :id, '--nicpromisc1', 'allow-all']
  end
end
```





## 安裝 Kubeadm Kubectl Kubelet 

```bash=
export KUBE_VERSION="1.17.0"
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubeadm=${KUBE_VERSION}-00 kubelet=${KUBE_VERSION}-00 kubectl=${KUBE_VERSION}-00
```


Service
![](https://i.imgur.com/2HFKw9C.png)


初始化 Kubernetes Cluster

CNI 使用fannel
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```


```bash=
mkdir -p $HOME/.kube
# 設定檔(TOKEN,驗證)
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

```bash=
# 多節點加入可以使用
kubeadm join 10.0.2.15:6443 --token h4927u.xc4i5epds3aw0kbs \
    --discovery-token-ca-cert-hash sha256:92787d0bba51786f879304229b07e2a58878bc775cbf12a6f9bf4e95a8794f97 

```


![](https://i.imgur.com/ubpx4KH.png)



![](https://i.imgur.com/oecoyGJ.png)
coredns pending 原因是因為 CNI 還沒安裝，安裝Flannel (CNI)就會自動執行。


/etc/kubernetes/manifests kubeadmin 安裝玩會放對應設定到這個路徑，啟動 kubelet會參照這邊設定。

## kubeadm(快速部屬工具)

Static /Dynamic 兩種安裝方式



```bash=
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

設定檔

```bash=
kubectl -n kube-system get pods
```


## 取得資訊 

### Summary

幫 Bash 設定 kubectl 自動補齊
```bash=
source <(kubectl completion bash)
```


```bash=
kubectl get $resource_type $name
```


```bash=
kubectl -n kube-system get pods
```


### wide


### describe 

檢查常用這個指令

```bash=
kubectl -n kube-system describe pods xxxx

```


### Cluster Info

```bash=
kubectl version
# 叢集資訊
kubectl cluster-info
kubectl top node

kubectl top pod

kubectl api-versions
```

## Interacting with Container

kubectl logs $name

kubectl -n kube-system -f logs $name


- [boz/kail: kubernetes log viewer](https://github.com/boz/kail)
- [wercker/stern: ⎈ Multi pod and container log tailing for Kubernetes](https://github.com/wercker/stern)



## 複製檔案


```bash=
kubectl -n kube-system cp xxx node:ooo


kubectl -n kube-system exec _pod_name_ ls

kubectl -n kube-system exec _pod_name_ ip addr


kubectl -n kube-system exec -it _pod_name_  sh
```


### port-forward

```bash=
sudo kubectl -n kube-system port-forward pod/coredns-xxxx 53:53
```

## vagrant snapshot

每次 vagrant destroy 真的要花滿多時間，之前跟朋友聊到 snapshot(快照)回復VM會比較快，檔案也不會吃很多。我之前沒這麼使用過，今天花點時間測試。

大量指令參照[Vagrant Boxes - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10229090)這篇文章。

更多操作可以看[Vagrant 學習筆記 - VagrantPi Blog](https://vagrantpi.github.io/2018/02/11/vagrant/)

**這邊注意 `vagrant destroy`會連帶刪除 snapshot**
**這邊注意 `vagrant destroy`會連帶刪除 snapshot**
**這邊注意 `vagrant destroy`會連帶刪除 snapshot**

### vagrant snapshot 

我對 snapshot 很陌生，但沒想到使用上非常方便。

### list

顯示有哪些 snapshot

```bash=
vagrant snapshot list
```
![](https://i.imgur.com/FYNDADm.png)


### save

儲存當下 vm snapshot。

```bash=
vagrant snapshot save k8s(這邊是放 snapshot 名稱)
```

![](https://i.imgur.com/0g6mhmx.png)

### restore

回原 vm snapshot。

```bash=
vagrant snapshot restore k8s(snapshot name)
```


![](https://i.imgur.com/pEYjMKI.png)


### delete

刪除 vm snapshot。

```bash=
vagrant snapshot delete k8s(snapshot name)
```


### push,pop

這邊跟 Git stash push/pop 很像，但我目前想不到什麼時候會用到這個，好處不需要想名稱，哈哈。
注意這邊不會影響到`vagrant snapshot save`指令新增的 snapshot。

```bash=
vagrant snapshot push
```

![](https://i.imgur.com/gyPYAxD.png)


```bash=
vagrant snapshot pop
```

![](https://i.imgur.com/rbNlZfG.png)


## vagrant port-forward

[Vagrant forward a range of ports - Server Fault](https://serverfault.com/questions/519429/vagrant-forward-a-range-of-ports)
[Forwarded Ports - Networking | Vagrant by HashiCorp](https://www.vagrantup.com/docs/networking/forwarded_ports)


## 備份

[wordpress - Correct way to back-up and restore vagrant box + Variable VVV - Stack Overflow](https://stackoverflow.com/questions/43411734/correct-way-to-back-up-and-restore-vagrant-box-variable-vvv)


[使用 vagrant package 備份指令打包 VirtualBox 環境 - TerryL](https://terryl.in/zh/vagrant-package-virtualbox-backup/)