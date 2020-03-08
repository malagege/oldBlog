---
title: Kubernetes 雜記
date: 2020-01-12 21:41:50
tags: [k8s]
categories: Kubernetes
---

尚未整理完成

<!--more-->

## 安裝 minikube

必備安裝
- Virtualbox
- minikube
- kubectl

### kubectl

單節點

[ (這邊請去官網看OS載點替換) Install and Set Up kubectl - Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

這邊我選 Linux
```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl (這邊請去官網看OS載點替換)
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

### Minikube


[(這邊請去官網看OS載點替換) kubernetes/minikube](https://github.com/kubernetes/minikube/releases)

```
curl -LO https://github.com/kubernetes/minikube/releases/download/v1.6.2/minikube-linux-amd64(這邊請去官網看OS載點替換)
chmod +x minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
```

### minikube start

```
minikube start                                                                             
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

## 遇到錯誤

### VM 64 bit 不能安裝 64 bit OS
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


### Exec format error

若有這個錯誤訊息...

> bash: /usr/local/bin/kubectl: cannot execute binary file: Exec format error

就是安裝錯版本

## 刪除 minikube

Delete minikube

minikube delete && rm -rf ~/.minikube


## Vagrant+Virtailbox 架設環境

安裝
- Vagrant
- Virtalbox

git clone [pires/kubernetes-vagrant-coreos-cluster: Kubernetes cluster (for testing purposes) made easy with Vagrant and CoreOS.](https://github.com/pires/kubernetes-vagrant-coreos-cluster)

上面會遇到如下問題
```
WARNING: Vagrant has detected the `vagrant-triggers` plugin. This plugin conflicts
with the internal triggers implementation. Please uninstall the `vagrant-triggers`
plugin and run the command again if you wish to use the core trigger feature. To
uninstall the plugin, run the command shown below:

  vagrant plugin uninstall vagrant-triggers

Note that the community plugin `vagrant-triggers` and the core trigger feature
in Vagrant do not have compatible syntax.

To disable this warning, set the environment variable `VAGRANT_USE_VAGRANT_TRIGGERS`.
Installed Version: 2.2.4
Latest Version: 2.2.6
```
但好像不用理這個


[ecomm-integration-ballerina/kubernetes-cluster: Kubernetes cluster using Vagrant, VirtualBox and Kubeadm](https://github.com/ecomm-integration-ballerina/kubernetes-cluster)
[Multi Node Kubernetes Cluster with Vagrant, VirtualBox and Kubeadm](https://medium.com/@raj10x/multi-node-kubernetes-cluster-with-vagrant-virtualbox-and-kubeadm-9d3eaac28b98)

別的也有這個[kubernetes-vagrant-centos-cluster/README-cn.md at master · rootsongjc/kubernetes-vagrant-centos-cluster](https://github.com/rootsongjc/kubernetes-vagrant-centos-cluster/blob/master/README-cn.md)

vagrant up 

會遇到錯誤
```
...
==> master: Running triggers before up...
==> master: 2020-01-14 22:11:00 +0800: setting up Kubernetes master...
==> master: Setting Kubernetes version 1.10.9
==> master: Box 'coreos-alpha' could not be found. Attempting to find and install...
    master: Box Provider: virtualbox
    master: Box Version: = 2345.0.0
==> master: Loading metadata for box 'http://alpha.release.core-os.net/amd64-usr/current/coreos_production_vagrant.json'
    master: URL: http://alpha.release.core-os.net/amd64-usr/current/coreos_production_vagrant.json
==> master: Adding box 'coreos-alpha' (v2345.0.0) for provider: virtualbox
    master: Downloading: https://alpha.release.core-os.net/amd64-usr/2345.0.0/coreos_production_vagrant.box
    master: Calculating and comparing box checksum...
==> master: Successfully added box 'coreos-alpha' (v2345.0.0) for 'virtualbox'!
==> master: Importing base box 'coreos-alpha'...
==> master: Matching MAC address for NAT networking...
==> master: Checking if box 'coreos-alpha' version '2345.0.0' is up to date...
==> master: Setting the name of the VM: kubernetes-vagrant-coreos-cluster_master_1579011179473_57219
It appears your machine doesn't support NFS, or there is not an
adapter to enable NFS on this machine for Vagrant. Please verify
that `nfsd` is installed on your machine, and try again. If you're
on Windows, NFS isn't supported. If the problem persists, please
contact Vagrant support.
```

解決方是

```
 sudo apt-get install nfs-common nfs-kernel-server
```

[Simplified install with vagrant_xuxiao1991101的博客-CSDN博客](https://blog.csdn.net/xuxiao1991101/article/details/50563329)






##