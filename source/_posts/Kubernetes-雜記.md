---
title: Kubernetes 雜記
date: 2020-01-12 21:41:50
tags: [k8s]
categories: Kubernetes
---

尚未整理完成

<!--more-->



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