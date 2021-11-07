---
title: Electron 使用 vue router 開啟視窗方法
date: 2021-11-07 22:10:24
tags: [electron]
categories: Nodejs
---

最近發生蠢事，我原本看到網路上使用 Electron 可以用 Browserwindow 控制視窗內容，但我發現網路方法竟然都失效，結果我爬到一篇發現是我犯蠢了，[vue electron 创建子窗口后内容和父窗口一样 配置vue-router无效？ - SegmentFault 思否](https://segmentfault.com/q/1010000040297139)跟裡面作者一樣忘記加 `<router-view>`。

<!--more-->

## Electron 設定 vue-router 開啟視窗方法

1. vue-router 改成 hash模式

2. 設定 Browserwindow.loadURL

```javascript=
    const pageUrl = import.meta.env.MODE === 'development' && import.meta.env.VITE_DEV_SERVER_URL !== undefined
    ? import.meta.env.VITE_DEV_SERVER_URL+`#/${hash || ''}${urlParam}`
    : new URL(`../renderer/dist/index.html#/${hash || ''}${urlParam}`, 'file://' + __dirname).toString();

    notifyWindow.loadURL(pageUrl);
```

3. `<router-view></router-view>`，很多教學沒有這段，但漏這段就是出不來XD


相關程式可以看[修改成 router · malagege/vue-electron-Pomodoro@5786989 · GitHub](https://github.com/malagege/vue-electron-Pomodoro/commit/5786989425c1b107f2fe4c881d25d94adde40fce)