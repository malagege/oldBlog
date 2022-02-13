---
title: Vagrant 關機沒用到的機器
date: 2022-02-13 22:52:32
tags: [vagrant,shell]
categories: Vagrant
---
之前有看到有一篇說到使用 Vagrant 時，要把其他啟用 Vagrant 關閉，因為有可能跟其他機器衝突。最近想關閉其他機器，所以找了一次關機所有機器方法。順便留著紀錄。

<!--more-->

```bash=
# 看所有機器狀態
vagrant global-status]
# 開啟機器做關機
vagrant global-status | awk '/running/{print $1}' | xargs -r -d '\n' -n 1 -- vagrant suspend
```
參考: [Shutting down all VirtualBox (vagrant) VMs in one easy to use bash command (that can be put into a bash file) - Ask Ubuntu](https://askubuntu.com/questions/457329/shutting-down-all-virtualbox-vagrant-vms-in-one-easy-to-use-bash-command-that)