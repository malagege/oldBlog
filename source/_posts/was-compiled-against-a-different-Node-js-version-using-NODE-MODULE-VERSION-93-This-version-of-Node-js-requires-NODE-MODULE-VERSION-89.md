---
title: >-
  was compiled against a different Node.js version using NODE_MODULE_VERSION 93.
  This version of Node.js requires NODE_MODULE_VERSION 89.
date: 2021-11-07 22:02:15
tags: [nodejs,electron,winapi]
categories: Nodejs
---

最近在重製番茄鐘，原本之前串接[electron使用winapi筆記 | 程式狂想筆記](https://malagege.github.io/blog/2018/10/19/electron%E4%BD%BF%E7%94%A8winapi%E7%AD%86%E8%A8%98/)，想說之前做過，最後在做這個，今天串接 Desktop-Idle 發生問題，發了一些時間處理。

都忘記當初有用 VM 跑做這段😅

```bash=
npm install --global --production windows-build-tools
```

最後沒安裝成功，直接跑去手動安裝。於是遇到標題問題。

這個錯誤很多網站解法都無法修正，也許這個方法不一定會成功，但希望能幫到大家。

<!--more-->

這問題是甚麼原因我還沒釐清楚

![](https://i.imgur.com/klFLlLk.png)

```
下午11:48:29 [main] Error: The module '\\?\F:\vue-electron-Pomodoro\node_modules\desktop-idle\build\Release\desktopIdle.node'
was compiled against a different Node.js version using
NODE_MODULE_VERSION 93. This version of Node.js requires
NODE_MODULE_VERSION 89. Please try re-compiling or re-installing
the module (for instance, using `npm rebuild` or `npm install`).
    at process.func [as dlopen] (electron/js2c/asar_bundle.js:5:1846)
    at Object.Module._extensions..node (internal/modules/cjs/loader.js:1143:18)
    at Object.func [as .node] (electron/js2c/asar_bundle.js:5:1846)
    at Module.load (internal/modules/cjs/loader.js:940:32)
    at Module._load (internal/modules/cjs/loader.js:781:14)
    at Function.f._load (electron/js2c/asar_bundle.js:5:12913)
    at Module.require (internal/modules/cjs/loader.js:964:19)
    at require (internal/modules/cjs/helpers.js:88:18)
    at Object.<anonymous> (F:\vue-electron-Pomodoro\node_modules\desktop-idle\index.js:1:21)
    at Module._compile (internal/modules/cjs/loader.js:1083:30)
```

解決方案參考: https://github.com/serialport/node-serialport/issues/1910#issuecomment-524949720


Steps:

    1. If you have not installed electron-rebuild just install it with the command: npm i -D electron-rebuild
    2. Remove from the node-modules folder the serialport and @serialport folders.
    3. Remove the file packages-lock.json
    4. Run npm i to install non-installed modules
    And finally run ./node_modules/.bin/electron-rebuild

    It is very important to run ./node_modules/.bin/electron-rebuild directly after npm i.

簡單來說就是
1. 刪除 node-modules
2. 刪除 packages-lock.json
3. npm install
4. 確認 package.json 有無加 version (原因如下)
5. ./node_modules/.bin/electron-rebuild



要確定 package.json 原因
![](https://i.imgur.com/eKoT88e.png)

package.json 補上 version，就可以順利執行。

![](https://i.imgur.com/GeBuSjL.png)


![](https://i.imgur.com/oGRRHtW.png)

`npm run watch` 執行就可以正常開啟。

目前猜測是跟electron-builder 有關係，但為什麼執行`./node_modules/.bin/electron-rebuild`就可以跑?感覺跟Elecgtron 編譯環境有關係的樣子。


找了很多篇無法成功，我還以為無法突破這個錯誤😬😬😬，好險解決了。


[環境 | Electron + Python + Vue. 採坑筆記 | by d.l.spm | Medium](https://cbb104002.medium.com/%E7%92%B0%E5%A2%83-electron-python-vue-fa164eb20250)

## Github Action遇到錯誤


![](https://i.imgur.com/yxbGnV8.png)

安裝跑出一樣的問題。`./node_modules/.bin/electron-rebuild`不能執行，最後下`npx electron-rebuild`就可以跑了。


[排除 was compiled against a different Node.js version using NODE_MODUL… · malagege/vue-electron-Pomodoro@13ebc90](https://github.com/malagege/vue-electron-Pomodoro/commit/13ebc90deb1e633fb2614f9cc0677b6e858b198c)

![](https://i.imgur.com/eIUjrb7.png)

