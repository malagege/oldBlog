---
title: Electron Autoupdater 程式自動更新方法 & GITHUB 更新方法
date: 2021-11-07 22:07:16
tags: [electron,autoupdater,github,action]
categories: Nodejs
---

大部分教學都不太一樣，後來發現 Electron-builder 的 Autodater 跟 Electron 的 Autoupdater 不太一樣。Electron 內建必須做 codesign 簽章，但不知道能不能用 mkcert ，但感覺不太能做的樣子。avast 一直對我build 出來 msi,exe 做掃描，聽說 codesign 能改善這個問題? codesign 感覺也是大坑，這篇先主要把我網路上整理資料先為主，先以 electron-builder為主。


<!--more-->

## 相關爬的文章整理

- [Electron auto update introduction](https://philo.dev/electron-auto-update-explained/)


- [electron/update-electron-app: 🌲 A drop-in module that adds autoUpdating capabilities to Electron apps](https://github.com/electron/update-electron-app)


- [avocode/electron-windows-autoupdate: Get Electron AutoUpdater working on Windows](https://github.com/avocode/electron-windows-autoupdate)

- [electron-builder+electron-updater实现应用自动更新过程 - 简书](https://www.jianshu.com/p/56f0b4bad689)

- [Electron應用使用electron-builder配合electron-updater實現自動更新 - SegmentFault 思否](https://segmentfault.com/a/1190000012904543)

> 注意：這個autoUpdater不是electron中的autoUpdater，是electron-updater的autoUpdater，否則坑你沒商量！

- [[ Day 35 ] - Electron 應用程式 - 更新自動化 ( 說明篇 ) - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10253168)

- [electron-builder-auto-update-example/src at master · alondanoch/electron-builder-auto-update-example](https://github.com/alondanoch/electron-builder-auto-update-example/tree/master/src)


- [electron-autoupdate-scaffold/README.md at master · ganyouyin/electron-autoupdate-scaffold](https://github.com/ganyouyin/electron-autoupdate-scaffold/blob/master/README.md)

- [electron/code-signing.md at main · electron/electron](https://github.com/electron/electron/blob/main/docs/tutorial/code-signing.md)

- [[ Day 35 ] - Electron 應用程式 - 更新自動化 ( 說明篇 ) - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10253168)

## electron-builder autoupdater 一般更新

非常推薦看[Electron自动更新 - 王叨叨](https://wangdaodao.com/20210204/electron-updater.html)([備份圖](https://i.imgur.com/189uSRJ.png))這篇，裡面有詳細介紹各種寫法，還有demo。

[使用 electron-builder 及 electron-updater 给项目配置自动更新_一片天空-CSDN博客](https://blog.csdn.net/daipianpian/article/details/105706814)

[electron-updater更新electron应用程序 - 簡書](https://www.jianshu.com/p/15bde714e198)

裡面還有提到electron-log日志，不知道有沒有機會使用到???

這邊就直接用第一篇文章範例直接操作。
git clone [wangdaodao/vue-electron-template](https://github.com/wangdaodao/vue-electron-template) 下來，裡面都已經設定好。

調整項目

1. vue.config.js 更新網址改成本機 IP 就可以更新。
![](https://i.imgur.com/Vrgie9v.png)


2. dist_electron 改成 download HFS 更新，這邊你可以用其他 HTTP Server 都可以。
![](https://i.imgur.com/BTYRhUU.png)

### Github更新 Electron 程式

[Publish - electron-builder](https://www.electron.build/configuration/publish#githuboptions)

更新官方有幾種方式，有支援AWS空間，其中讓我感興趣是 github 。私有的專案也可以用的，但不知道會不會有什麼限制。文件有提到可以用 Github Actions 發布，我突然想到我的[malagege/vue-electron-Pomodoro](https://github.com/malagege/vue-electron-Pomodoro)參考[cawa-93/vite-electron-builder: Secure boilerplate for Electron app based on Vite. TypeScript + Vue/React/Angular/Svelte/Vanilla](https://github.com/cawa-93/vite-electron-builder)專案做的，裡面有設定 Github Actions。看文章說執行完會在 Github Release 建立草稿，我看之前專案有 build 成功過。

![](https://i.imgur.com/B78YXXn.png)

我看Release真的有建立。

![](https://i.imgur.com/VBdRmlk.png)


題外話，這邊發現我Github Actions 後面都錯誤，但大概猜到是因為使用 desktop-idle 關係造成的，最後有排除。[github actions 失敗問題 · malagege/vue-electron-Pomodoro@e3ffacf](https://github.com/malagege/vue-electron-Pomodoro/commit/e3ffacfcc2cb31a40bc9ca222bd2fb56151157b3)


~~最後排除掉，就是設定 provide 設定成 Github，其它要設定什麼，當出我也很疑惑，我這邊卡很久再研究文件，最後發現 Github Actions 會帶 github token ，也會帶 Repo 名稱參數，所以只要設設定`設定 provide 設定成 Github`。~~我怎麼沒先動手試試，這邊看了一段時間，哈哈。

我後來用沒設定前一版沒做 Github 設定，竟然會更新，太神奇了。可能 Action 那一段有做到更新。

electron.builder.config.js
![](https://i.imgur.com/j2CHSTy.png)

打開舊版APP就會跳出更新就是成功了。這邊更新會等關掉程式後更新，還算滿人性化更新。另外，我參考原本程式就有做這段。

```javascript=
// Auto-updates
if (import.meta.env.PROD) {
  app.whenReady()
    .then(() => import('electron-updater'))
    .then(({autoUpdater}) => autoUpdater.checkForUpdatesAndNotify())
    .catch((e) => console.error('Failed check updates:', e));
}


```

![](https://i.imgur.com/kV1qGPZ.png)

[Creating and deploying an auto-updating Electron app for Mac and Windows using electron-builder | by John Dyer | Medium](https://medium.com/@johndyer24/creating-and-deploying-an-auto-updating-electron-app-for-mac-and-windows-using-electron-builder-6a3982c0cee6)


## 手動執行更新


[Electron 十问十答（2） | Beace Blog](https://beacelee.com/2019/05-13/electron-2/)
[[electron]終極奧義 五千字教程丟給你 - IT閱讀](https://www.itread01.com/xxcp.html)
[Electron应用实现自动更新 - 知乎](https://zhuanlan.zhihu.com/p/349508847)
[Electron Auto-Updater via Renderer (Frontend) Manipulation | by Meriç Melike Yılmaz | Medium](https://mmelikes.medium.com/electron-auto-updater-with-frontend-manipulation-33d3bc5057f3)
[electron + nodejs (热更新)_这么近又那么远的博客-CSDN博客](https://blog.csdn.net/qq_32900003/article/details/106282787)

大部分教學都沒有用 bridge 串，所以必須用 ipcmain 去接收是否要做更新。

通過選單更新[electron-builder/encapsulated manual update via menu.js at a94532164709a545c0f6551fdc336dbc5377bda8 · electron-userland/electron-builder · GitHub](https://github.com/electron-userland/electron-builder/blob/a94532164709a545c0f6551fdc336dbc5377bda8/docs/encapsulated%20manual%20update%20via%20menu.js#L56)



## 了解 AutoUpdater 函式/事件


### 事件(Event)

#### error

中間發生錯誤觸發 error Event。

#### checking-for-update

更新開始後，馬上觸發這個 Event。

#### update-available

檢查到可以更新後，觸發這個 Event。如果`autoDownload`設定 `true`將會自動下載更新程式。

參數:
info UpdateInfo (for generic and github providers) | VersionInfo (for Bintray provider)

#### update-not-available

沒有檢查到更新，觸發這個 Event。

#### download-progress

下載時候觸發Event?

#### update-downloaded

下載完成觸發Event。

### Method

#### checkForUpdates ⇒ `Promise<UpdateCheckResult>`

請求Server看是不是有更新。

#### checkForUpdatesAndNotify ⇒ `Promise<UpdateCheckResult>`(重點)

請求Server看是不是有更新，有的畫自動更新和發送通知。

Call `autoUpdater.checkForUpdatesAndNotify()`. Or, if you need custom behaviour, implement `electron-updater` events, check examples below.

這邊特別說明一下，下面通知是寫死固定，原本看文件以為沒辦法調整，不過看源碼可以帶參數修正文字訊息。
![](https://i.imgur.com/kV1qGPZ.png)

```javascript=
if (import.meta.env.PROD) {
  app.whenReady()
    .then(() => import('electron-updater'))
    .then(({autoUpdater}) => autoUpdater.checkForUpdatesAndNotify({
      title: '程式已經準備好更新',
      body: `{appName} 版本 {version} 已經下載完成，重開應用程式可進行安裝`,
    }))
    .catch((e) => console.error('Failed check updates:', e));
}
```

[electron-builder/AppUpdater.ts at f45110cbf66572d5748d21fc24dc26cabd06f35f · electron-userland/electron-builder](https://github.com/electron-userland/electron-builder/blob/f45110cbf66572d5748d21fc24dc26cabd06f35f/packages/electron-updater/src/AppUpdater.ts)

彩蛋錯誤

```javascript=
ipcMain.on('check-update', function(){
  // import {checkForUpdates} from './updater.js'; // 會發生  error  Parsing error: 'import' and 'export' may only appear at the top level
  (() => import('./updater.js'))().then(({checkForUpdates})=> checkForUpdates());
});

```

[vue CLI异步组件报错import' and 'export' may only appear at the top level_郑伟斌的博客-CSDN博客](https://blog.csdn.net/qq_26909801/article/details/105970679)

這篇有空再整理 import 用法(有時間的話，哈哈)

#### downloadUpdate

`autoDownload = false` 自動下載關閉，可以使用這個method下載。

#### getFeedURL

設定 update provider

#### quitAndInstall

appUpdater.quitAndInstall(isSilent, isForceRunAfter)

關閉程式並且更新。

```
Restarts the app and installs the update after it has been downloaded. It should only be called after update-downloaded has been emitted.

Note: Like Electron’s built-in quitAndInstall method, autoUpdater.quitAndInstall() will close all application windows first and only emit before-quit event on app after that. This is different from the normal quit event sequence (i.e. not during an update). As a result you should listen to Electron’s before-quit-for-update event (on electron.autoUpdater) if you wish to perform actions before the windows are closed while a process is quitting, as well as listening to before-quit. However, that is not the case for the (Windows) NSIS or (Linux) AppImage targets. The before-quit-for-update event is not emitted. The before-quit event is emitted first, then windows will be closed.

isSilent Boolean - windows-only Runs the installer in silent mode. Defaults to false.
isForceRunAfter Boolean - Run the app after finish even on silent install. Not applicable for macOS. Ignored if isSilent is set to false.
```


### 結論

本篇採用 electron-builder 簡單實作用 Github 手動/自動更新程式，這邊設定只是基本調整，其實還有滿多東西沒有整理出來，因為是簡單研究，所以就打算繼續深入，相信以後專案需要做單機版我就可以考慮採用這方案去進行。
