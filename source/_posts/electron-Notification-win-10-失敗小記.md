---
title: electron Notification win 10 失敗小記
date: 2018-09-30 21:23:01
tags: [electron,javascript,notification]
categories: JavaScruot
---

執行失敗，要算在復仇名單?
還是放棄orz

<!--more-->

[node-notifier - 节点通知模块的问题](https://stackoverrun.com/cn/q/12037330)
查到好像跟node-notifier有關係
```
SnoreToast.exe -p C：\Projects\testSnoreToast\coulson.png -m Hello -t World -s Notification.Default -appID
```
貼到win10 cmd不能跑
```
SnoreToast.exe -m Hello -t World
```
就會正常看到通知了

我測試我寫的vue-electron 竟然不能跑node-notifier
但網路上有找到
[explooosion/electron-alarm-clock: Sample apps for Electron](https://github.com/explooosion/electron-alarm-clock)
抓下來可以正常執行...(electron 2.0~3.0也試過，範例都能跑，看來跟版本無關)

目前懷疑是vue cli 3 electron環境執行node-notifier會有問題
不過...嘗試著N方法也不行
electron 2.0~3.0都試過了...

例如加上
`app.setAppUserModelId("com.electron.test1");`
也許要用甚麼註冊AppUserModelId才能跑?

ID好像有命名規則
CompanyName.ProductName.SubProduct.VersionInformation

但還是不行 orz
網路資源不多




Linux試過都正常，win10不知道為什麼那麼難搞
目前感覺原生electron 的Tray跟Notification  都在Win10失效了QQ