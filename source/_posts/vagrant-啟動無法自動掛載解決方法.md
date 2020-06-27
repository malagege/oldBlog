---
title: vagrant 啟動無法自動掛載解決方法
date: 2020-06-26 22:58:01
tags: [vagrant,virtualbox]
categories: Vagrant
---

最近發現 Ubuntu 某一版就不能做自動掛載
會出現
```
Vagrant was unable to mount VirtualBox shared folders. This is usually
because the filesystem "vboxsf" is not available. This filesystem is
made available via the VirtualBox Guest Additions and kernel module.
Please verify that these guest additions are properly installed in the
guest. This is not a bug in Vagrant and is usually caused by a faulty
Vagrant box. For context, the command attempted was:

mount -t vboxsf -o uid=1000,gid=1000 vagrant /vagrant

The error output from the command was:

/sbin/mount.vboxsf: mounting failed with the error: No such device
```

<!--more-->

其實只要安裝
vagrant plugin install vagrant-vbguest

就解決了