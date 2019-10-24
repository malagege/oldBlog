---
title: 啟用樹梅派 root 帳號
date: 2019-10-20 12:43:45
tags: [linux]
categories: Linux
---

最近要測試環境
拿我手上沒在跑的樹梅派開 root
沒想到有幾個流程
因為不常做，所以小記一下

<!--more-->

1. sudo passwd root
打密碼

2. sudo passwd --unlock root

3. sudo nano /etc/ssh/sshd_config
我沒看到 PermitRootLogin without-password 
新增 PermitRootLogin yes

4. reboot
重開機

這樣就能用 root 燈入了