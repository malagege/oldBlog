---
title: Electron 大雜燴文章區
date: 2022-07-31 02:21:00
tags: [nodejs,electron]
categories: JavaScript
---

尚未整理完成，有空整理。

<!--more-->

## 架構 & 生命週期

[cawa-93/vite-electron-builder: Secure boilerplate for Electron app based on Vite. TypeScript + Vue/React/Angular/Svelte/Vanilla](https://github.com/cawa-93/vite-electron-builder)
![](https://i.imgur.com/n6M86ZK.png)


[[ Day 3 ] - 桌面小圖示(二) - Electron 架構說明 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10233853)

![](https://i.imgur.com/bBvxe3o.png)


[Electron 生命週期看這篇就夠了_淘系前端團隊 - MdEditor](https://www.gushiciku.cn/pl/gcfd/zh-tw)

### preload.js

[contextBridge | Electron](https://www.electronjs.org/docs/api/context-bridge)

nodejs 跟 存 web 溝通頁面。

### main

nodejs 程式。

### render

web 程式。

就跟上面圖示差不多。




## Electron log

[GitHub - megahertz/electron-log: Just a simple logging module for your Electron application](https://github.com/megahertz/electron-log)


## Electron 安全性

- [electron的安全问题需要注意哪些问题？ - 知乎](https://www.zhihu.com/question/266893700)

- [安全，本地功能和你的责任 - Electron中文手册](https://yuzhigang5460.gitbook.io/electron/tutorial/security)

```
这并不是万无一失的，但至少，你应该尝试以下内容：
只显示安全的内容(https)
在显示远程内容的所有渲染器中禁用 Node 集成 (在 webPreferences 中设置 nodeIntegration 为 false)
在显示远程内容的所有渲染器中启用上下文隔离 (在 webPreferences 中设置 contextIsolation 为 true)
在所有加载远程内容的会话中使用 ses.setPermissionRequestHandler() .
不要禁用 webSecurity. 禁用它将禁用同源策略.
定义一个 Content-Security-Policy
, 并使用限制规则 (即： script-src 'self')
覆盖并禁用 eval
, 它允许字符串作为代码执行.
不要设置 allowRunningInsecureContent 为 true.
不要启用 experimentalFeatures 或 experimentalCanvasFeatures 除非你知道你在做什么.
不要使用 blinkFeatures 除非你知道你在做什么.
WebViews: 不要填加 nodeintegration 属性.
WebViews: 不要使用 disablewebsecurity
WebViews: 不要使用 allowpopups
WebViews: 不要使用 insertCSS 或 executeJavaScript 操作远程 CSS/JS.
WebViews: 在 will-attach-webview事件之前,应验证 <webview>所有的选项或参数:
```

目前看 render 程是不要擺上危險執行程式，產生出來 aser 可以被解壓縮出來，所以別人可以看到程式或調整程式。

prelaod.js 用到程式不確定會有問題，不過目前猜測應該不會有問題才是。main 那邊就不用說了，這邊是安全區域。


## Electron 開機啟動

[electron实现开机自启+通过链接调起应用(windows) - SegmentFault 思否](https://segmentfault.com/a/1190000018894782)


## Electron 注冊桌面軟體(protocol 模塊)

```
const { app, protocol } = require("electron");
const path = require("path");

app.on("ready", () => {
  protocol.registerFileProtocol(
    "uinika",
    (request, callback) => {
      const url = request.url.substr(7);
      callback({ path: path.normalize(`${__dirname}/${url}`) });
    },
    error => {
      if (error) console.error("協議註冊失敗！");
    }
  );
});

```

參考[使用Electron打造跨平台桌面應用 - 知乎](https://zhuanlan.zhihu.com/p/52991793)，裡面參滿多好康內容，推薦進去看。