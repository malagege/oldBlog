---
title: Vite 搭配 Electron 整理(不使用TypeScript)
date: 2021-10-09 23:23:32
tags: [vite,electron,javascript]
categories: Vite
---

# Vite 搭配 Electron 整理(不使用TypeScript)

1. [cawa-93/vite-electron-builder: Secure boilerplate for Electron app based on Vite. TypeScript + Vue/React/Angular/Svelte/Vanilla](https://github.com/cawa-93/vite-electron-builder)
2. [Vue3+Electron整合方式 - 知乎](https://zhuanlan.zhihu.com/p/181015456)

目前好像沒有官方推薦的，不過我選方案一，因為官方有特別註明怎麼調回 JavaScript，我 TypeScript 還沒有學。Orz


<!--more-->

## 調成 JavaScript

推薦看官網教學，也有可能因為時間更新而改變。

### 快速建置範本專案

[Create a New Repository from cawa-93/vite-electron-builder](https://github.com/cawa-93/vite-electron-builder/generate)

### 移除 JavaScript

[Guide to disable typescript and remove dependencies · Discussion #339 · cawa-93/vite-electron-builder](https://github.com/cawa-93/vite-electron-builder/discussions/339)

In project root:

1. Remove vetur.config.js
2. Remove .github/workflows/typechecking.yml
3. Remove types directory
4. Remove tsconfig.json directory

In package.json

1. Remove buildEnvTypes, pretypecheck and all typecheck* sctipts(我找不到...)

```
    "typecheck-main": "tsc --noEmit -p packages/main/tsconfig.json",
    "typecheck-preload": "tsc --noEmit -p packages/preload/tsconfig.json",
    "typecheck-renderer": "vue-tsc --noEmit -p packages/renderer/tsconfig.json",
    "typecheck": "npm run typecheck-main && npm run typecheck-preload && npm run typecheck-renderer"
```

```
    "pre-push": "npm run typecheck"
```

3. Remove simple-git-hooks.pre-push and run npx simple-git-hooks to update git-hooks internal config

```
    "pre-push": "npm run typecheck"
```

```bash=
npx simple-git-hooks
```

5. Remove unnecessary dependencies by:

```bash=
    npm uninstall @types/electron-devtools-installer @typescript-eslint/eslint-plugin typescript vue-tsc
```
In .eslintrc.json Remove:

    plugin:@typescript-eslint/recommended from extends
    parser
    parserOptions
    @typescript-eslint from plugins
    types/env.d.ts from ignorePatterns
    All @typescript-eslint/* from rules

下面不要拿掉，不然git commit 會有錯誤(eslint 會報錯)
```
  "parserOptions": {
    "ecmaVersion": 12,
    "sourceType": "module"
  },
```

In packages/main:

    Rewrite src/index.ts to src/index.js (改名)
    In vite.config.js change build.lib.entry to src/index.js
![Uploading file..._lm6h5zlqz]()
    
    Remove tsconfig.json

In packages/preload:

    Rewrite src/index.ts to src/index.js
    In vite.config.js change build.lib.entry to src/index.js
    Remove tsconfig.json
    Remove types directory

In packages/renderer:

    Rewrite all files in src from TS to JS. If you want use Vue, you need remove lang="ts" attribute in all .vue files.
    
![](https://i.imgur.com/AKV8C73.png)
    
    
    In index.html change script src attribute to you new entry file (it may be ./src/index.js for example).
    Remove tsconfig.json
    In .eslintrc.json remove parserOptions.parser
    Remove types directory



### ExtensionLoadWarning(無法解決)

[(node:19656) ExtensionLoadWarning: Warnings loading extension at · Issue #943 · nklayman/vue-cli-plugin-electron-builder](https://github.com/nklayman/vue-cli-plugin-electron-builder/issues/943)

```
npm install electron-devtools-installer --save-dev

```

```
const { default: installExtension, REACT_DEVELOPER_TOOLS } = require('electron-devtools-installer');
```

```javascript=
    try {
      await installExtension({
        id: 'ljjemllljcmogpfapbkkighbhhppjdbg', //Vue Devtools beta
        electron: '>=1.2.1'
      })
    } catch (e) {
      console.error('Vue Devtools failed to install:', e.toString())
    }
```


## 彩蛋

調整 Electron 視窗資料，實用功能

[MarshallOfSound/electron-devtools-installer: An easy way to ensure Chrome DevTools extensions into Electron](https://github.com/MarshallOfSound/electron-devtools-installer)

[如何实现 electron 窗体透明 transparent 效果？](https://newsn.net/say/electron-transparent.html)

transparent: true

拖移視窗
[electron程序，自定义标题栏的几个特殊css属性总结](https://newsn.net/say/electron-bar-css.html)

-webkit-app-region: drag;

[electron程序，如何隐藏窗体顶部菜单？](https://newsn.net/say/electron-no-application-menu.html)

1. autoHideMenuBar:true
2. frame:false

[Frameless window with controls in electron (Windows) - Stack Overflow](https://stackoverflow.com/questions/35876939/frameless-window-with-controls-in-electron-windows)


[electron - 无边框、最大化、最小化、等比缩放问题_wanzheng_96的博客-CSDN博客](https://blog.csdn.net/wanzheng_96/article/details/118026000)

[javascript - Electron require() is not defined - Stack Overflow](https://stackoverflow.com/questions/44391448/electron-require-is-not-defined)
[Electron 12+ 出現 require is not defined 報錯解決_Jesse_0 - MdEditor](https://www.gushiciku.cn/pl/gnPj/zh-tw)

electron - 无边框、最大化、最小化、等比缩放问题 無法使用在最新版程式
```javascript=
app.on('ready', () => {
    mainWindow = new BrowserWindow({
        webPreferences: {
            nodeIntegration: true,
            contextIsolation: false,
        }
    });
});
```

後來發現會現行的 RENDER 會有種突，所以還是不要這樣用。

解決方式
main 程式寫 electron 程式碼
![](https://i.imgur.com/tJSHHZT.png)

原Web 操作寫在 preload
![](https://i.imgur.com/kBOwa4q.png)

HTML(App.vue)可以這樣呼叫 preload 程式
![](https://i.imgur.com/YH5vA88.png)

有空在寫仔細，就怕沒控整理。先簡單記錄


[Electron 自动热更新丨阿西河](https://www.axihe.com/anbang/blog/electron/electron-automatic-hot-update.html)