---
title: XShell 7關掉圖形介面
date: 2022-07-31 01:29:32
tags: [xshell]
categories: XShell
---

## XShell 7連線屬性設定

如果有在用XShell 7做維運，連線屬性設定->連線->SSH->通道->X11轉寄[預設會勾選，建議取消勾選]


![](https://i.imgur.com/KFXhD2x.png)

因為當你用這個連線啟動的程式跟處理圖形處理有關時(產條碼or報表內部有用到…)，有勾選
X11轉寄的話會用到Linux的X-Windown(X11)，XShell 7會彈出這個訊息：
![](https://i.imgur.com/gjO6aT8.png)
轉檔程式就會出現類似這樣的錯誤：

![](https://i.imgur.com/PFr4bN4.png)
