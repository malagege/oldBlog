---
layout: post
title: '不用寫程式也能做Window Service(NSSM)'
date: 2017-12-23 15:19
comments: true
categories: 實用工具
tags: [Window Service,NSSM]
---
最近常常執行chat server，需要常常自己開
後來有時忘記開，怕之後有忘記開還有下面執行列多一個很討厭
所以我上網找了一下Window Service有什麼方法可以做
竟然看到不用寫程式，但前提是需要裝這個工具
不多說，先來筆記

<!--more-->

#指令
## 啟動服務
```sh
$ nssm start "服務名稱"
```
## 停止服務
```sh
$ nssm stop "服務名稱"
```
## 重啟服務
```sh
$ nssm restart "服務名稱"
```

## 刪除服務
```sh
$ nssm remove "服務名稱"
```

## 設定服務(中英文參照)

| 頁籤        | 參數              | 說明                       |
|-------------|-------------------|----------------------------|
| Application | Path              | 執行應用程式的路徑和名稱。 |
| Application | Startup Directory | 啟動資料夾。               |
| Application | Arguments         | 應用程式參數。             |
| I/O         | Output (stdout)   | 結果輸出檔案。             |
| I/O         | Error (stderr)    | 錯誤輸出檔案。             |

中間，有試著測試假如我撞到port，起動NSSM建立好的Server會如何??
什麼?竟然還繼續Work!!!後來發現原來是NSSM會執行執行程式
所以我關掉chat Server竟然還可以跑
NSSM設定還可以設定很多東西


# 參考來源：
* [NSSM - the Non-Sucking Service Manager](https://nssm.cc/)
* [Xian's 學習筆記: Nginx run as windows service](http://blog.developer.idv.tw/2016/08/nginx-run-as-windows-service.html)
* [解決問題 Windows Service (1) | Leo Yeh's Blog](http://leoyeh.me:8080/2017/07/26/%E8%A7%A3%E6%B1%BA%E5%95%8F%E9%A1%8C-Windows-Service-1/)
* [透過NSSM建立Windows Services | RiCo技術農場 - 點部落](https:/

