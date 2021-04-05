---
title: x86 機器 build arm Docker Image 方法
date: 2021-03-28 16:17:49
tags: [docker]
categories: Docker
---

最近要build arm image
但是...在Raspberry Pi build 的時間非常久
在想x86有沒有辦法做到
結果真得可以

<!--more-->


```bash
sudo apt-get update && sudo apt-get install -y --no-install-recommends qemu-user-static binfmt-support
```

參考:[【 Solutions 】解決在 x86 環境下無法 Build ARM 的 Docker Image](https://learningsky.io/docker-build-on-ubuntu-x86/)


試著build 一個Raspberry Pi image 沒有什麼問題