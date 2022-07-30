---
title: 快速建置 sftp server (SFTPGo)
date: 2022-07-31 01:11:54
tags: [sftp,golang]
categories: 實用工具
---

因為工作需求，測試環境沒有 sftp，目前先找簡易的 sftp 建置，有找到一個好用的 SFTPGo 工具，這邊簡單紀錄。

[Releases · drakkan/sftpgo](https://github.com/drakkan/sftpgo/releases)

[浮雲雅築: [研究] 免費 Free SFTP、FTPS Server](http://shaurong.blogspot.com/2019/01/free-sftpftps-server.html)

<!--more-->

## 設定檔

sftpgo.json 裡面很長，但簡單紀錄幾個比較重要設定，如Port

```json=
  },
  "httpd": {
    "bindings": [
      {
        "port": 8080, 
        "address": "",
        "enable_web_admin": true,
        "enable_web_client": true,
        "enable_https": false,
        "client_auth_type": 0,
        "tls_cipher_suites": [],
        "proxy_allowed": [],
        "hide_login_url": 0,
        "render_openapi": true,
        "web_client_integrations": []
      }
    ],
      
    ...
      
  "sftpd": {
    "bindings": [
      {
        "port": 2022,
        "address": "",
        "apply_proxy_config": true
      }
    ],
    "max_auth_tries": 0,
    "banner": "",
    "host_keys": [],
```

```bash=
sftpgo serve
```

仔細看[官方文件](https://github.com/drakkan/sftpgo/blob/main/README.zh_CN.md)，感覺滿多設定可以玩。
