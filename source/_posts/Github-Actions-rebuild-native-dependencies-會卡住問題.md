---
title: Github Actions  rebuild native dependencies 會卡住問題
date: 2021-11-07 22:00:50
tags: [nodejs,github,action]
categories: nodejs
---

我最近做 Electron 專案有用到 win32api，在Github Action編譯會卡住，原因不明，不過有找到解決方法。

細說我 Github Actions 狀況是跑到一半卡住，Action 會一直運行，想說放著讓他跑，但沒想到沒有成功，超過6小時 Github Action 把他關掉了。

<!--more-->

解決方法參考:[Electron-builder keeps rebuilding native dependency · Issue #5386 · electron-userland/electron-builder](https://github.com/electron-userland/electron-builder/issues/5386)

> You can disable rebuilding with npmRebuild: 'false' in the config - but it would definitely be ideal if Electron-Builder could detect the need and rebuild only if needed. We ended up disabling it, but that has its own issues like accidentally building with broken node deps.

設定加了這段真的可以跑。

`npmRebuild: 'false'` 

![](https://i.imgur.com/ziHqxBb.png)



GITHUB ACTION 應該就能順利。祝大家都順利。