---
title: 確認 apk 簽章方法
date: 2022-07-31 02:12:03
tags: [apk,android,signature]
categories: 實用工具
---

kemon Unite 無法玩，所以網路找到 apk檔案，不知道有沒有確認安全性方法。這邊就做個簡單整理。

先貼我找APK網站地方[4 個安全且知名的 APK 檔下載網站整理 - 電腦王阿達](https://www.kocpc.com.tw/archives/319405)，我這邊是使用 APKMirror。

總共有四個:
1. APKMirror
2. APKPure
3. Aptoide
4. APK Downloader



<!--more-->


## 線上確認簽章


[VirusTotal - Home](https://www.virustotal.com/gui/home/upload)
[APK Tools Online](https://apk.tools/tools/apk-verification/)
[Certificate info | sisik](https://sisik.eu/cert)

VirusTotal 檢查結果有一個有問題樣子
![](https://i.imgur.com/lbwuwOp.png)


## apksigner


[確認 APK 檔案是否為 release 版的方式](https://hulkyang.blogspot.com/2020/08/apk-release.html)
1. aapt
2. jarsigner
3. apksigner

```
apksigner verify jp.pokemon.pokemonunite_1.2.1.2-324739_minAPI19\(armeabi-v7a\)\(nodpi\)_apkmirror.com.apk                                                                     ✔  8  16:22:05 
DOES NOT VERIFY
ERROR: APK Signature Scheme v2 signer #1: Malformed additional attribute #1
```

有找到相關驗證成功呈現內容。
[Android 如何检查apk是否已经签名，V1、V2、V3和V4签名？ - 行走的思想 - 博客园](https://www.cnblogs.com/onelikeone/p/15010397.html)
![](https://i.imgur.com/WXO2QXe.png)

```
apksigner verify -v --print-certs apkmname
```



[玩转 Android APK 签名，是时候该用上 apksigner 工具给你的 APP 签名了](https://bin.zmide.com/?p=879)


這邊怎麼測試都失敗，所以就不安裝了...

## 其他相關

### apk-info


[APK 資訊檢查工具《APK-Info》安裝第三方軟體時先行檢查，增強安全性 | 就是教不落 - 給你最豐富的 3C 資訊、教學網站](https://steachs.com/archives/3282?fbclid=IwAR1uqDC6mVFIsHETRPF0VFXSFk6TIOq9mEdduutX-KHqZJnQO6beqXAYJhw)
[APK-Info (Windows App) | XDA Forums](https://forum.xda-developers.com/t/apk-info-windows-app.2359373/)

![](https://i.imgur.com/vC1iWAi.png)

我看有那麼多Permissions ，我也不知道是不是安全的。



```
write_external_storage
read_external_storage
internet
vibrate
access_network_state
access_wifi_state
foreground_service
modify_audio_settings
record_audio
bluetooth
bluetooth_admin
wake_lock
jp.pokemon.pokemonunite.permission.c2d_message
com.android.vending.billing
com.google.android.finsky.permission.bind_get_install_referrer_service
com.google.android.c2dm.permission.receive

```


### apk 下載

[[教學] Google Play 商店如何跨區/國家限制，下載區域限定版遊戲軟體？ - 簡單生活Easylife](https://sofun.tw/google-play-account-country/)