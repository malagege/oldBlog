---
layout: post
title: '[phonegap]編譯APK錯誤訊息'
date: 2014-12-15 06:32
comments: true
categories: Phonegap
---
因為`phonegap build android`顯示需要Android SDK 19，但是卻出現這個訊息。

`The SDK Build Tools revision (19.0.3) is too low for project 'Testing'.
Minimum required is 19.1.0`

開起SDK Manager.exe選擇Tool資料夾裡面的Android SDK Build-tools (Rev)19.1

就解決此問題