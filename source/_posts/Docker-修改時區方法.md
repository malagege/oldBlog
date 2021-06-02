---
title: Docker 修改時區方法
date: 2021-06-02 20:59:48
tags: [docker]
categories: Docker
---

比較常在網路上看到

<!--more-->

```yaml
volumes: 
  - "/etc/localtime:/etc/localtime:ro"
```

但測試 Java Container發現沒有成功
後來使用下面方法就成功了

```yaml
    environment: 
      - "TZ=Asia/Taipei"
```

參考:

[設定 Docker Container 與 Host 相同時區的方法](https://www.arthurtoday.com/2016/07/how-to-setup-docker-container-timezone-host.html)

[[筆記] 修改 docker 容器內的時區 - Change Timezone in Docker | MC部落](https://hu.mc4.us/post/change-timezone-in-docker/)