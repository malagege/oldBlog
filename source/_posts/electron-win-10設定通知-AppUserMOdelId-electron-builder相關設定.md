---
title: electron win 10設定通知(AppUserModelId)&electron-builder相關NSIS設定
date: 2018-10-01 20:35:48
tags: [electron,notification]
categories: JavaScript
---

解決上一篇遇到win10通知沒法出來
[electron Notification win 10 失敗小記 | 程式狂想筆記](https://malagege.github.io/blog/2018/09/30/electron-Notification-win-10-%E5%A4%B1%E6%95%97%E5%B0%8F%E8%A8%98/)
最後仔細看了官方文件
才解決.....

<!--more-->

首先，win8 之後應用程式做通知都需要註冊`AppModelUserId`
在dev時候很難註冊到(沒有方法)
在查詢一堆資料(NSIS....)
發現需要做安裝動作
才能註冊`AppModelUserId`

node-notifier是透過SnoreToast.exe程式呼叫
所以其實也可以不用安裝這個

首先來看官方AppUserModelId設定
檔案請設定package.json(不是設定electron_dist裡面的)
```json
{
    ...,(第一階層)
    "build": {
    "appId": "com.example.app"
    }
}
```

但可以注意到[Common Configuration - electron-builder](https://www.electron.build/configuration/configuration#configuration)
```
Configuration¶

    appId = com.electron.${name} String - The application id. Used as CFBundleIdentifier for MacOS and as Application User Model ID for Windows (NSIS target only, Squirrel.Windows not supported). It is strongly recommended that an explicit ID is set.
    productName String - As name, but allows you to specify a product name for your executable which contains spaces and other special characters not allowed in the name property.
    copyright = Copyright © year ${author} String - The human-readable copyright line for the app.
```
appId預設有設定
所以其實用預設可以不用再package.json設定

最後起在background.js裡面加入`app.setUserModelId("com.electron.exampleName")`，最後exampleName改成package.json專案名子(name)

之後安裝，執行程式可以看到通知結果

NOTE: 通知安裝完，不會馬上在控制台看到程式設定
要第一次通知才能看到
{% asset_img 1.png "Win10控制通知設定" %}


## NSIS  取消一件安裝、設定安裝路徑

```json
  ...,
  "build":{
    "nsis":{
      "oneClick" : false,
      "allowToChangeInstallationDirectory": true
    }
  }
  
```
oneClick是否為一鍵安裝
allowToChangeInstallationDirectory是否讓User改安裝目錄

更多設定我就不寫了
[Common Configuration - electron-builder](https://www.electron.build/configuration/configuration#configuration)

未來有空可以研究更新
不過目前這個用不到
先留者記錄
[Electron应用使用electron-builder配合electron-updater实现自动更新 - 个人文章 - SegmentFault 思否](https://segmentfault.com/a/1190000012904543)