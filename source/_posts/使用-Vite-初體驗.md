---
title: 使用 Vite 初體驗
date: 2021-06-02 21:07:44
tags: [vue,vite,cors]
categories: Vue
---

我很久沒寫Vue
都忘記差不多
最近想寫一個小東西
有聽過一場Vite研討會
覺得很方便，就來使用看看


<!--more-->

[vite2: Make Web Dev Fast Again & Again. – Dropbox Paper](https://paper.dropbox.com/doc/vite2-Make-Web-Dev-Fast-Again-Again.-9DcJOLb0spaYNbCbF6Zqd)

裡面dev proxy，還滿方便的
但不能做CROS 方法
這邊想成`npm run dev` 時候，進去  /api/ 時候會倒 proxy server
測試時候很方便

除了上面教學

這邊還滿多 Vite 文章
[标签：vite - Fidel Yiu | 前端技术博客](https://www.kuxiaoxin.com/tags/vite)

Vite PWA
[第六十章-Vite插件-vite-plugin-pwa - Fidel Yiu | 前端技术博客](https://www.kuxiaoxin.com/archives/75)

但我後來源參照這篇

官方設定好像不是這樣(不知道是不是舊版的文)
設定非常簡單

```javascript= vite.config.js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { VitePWA } from 'vite-plugin-pwa'

// https://vitejs.dev/config/
export default defineConfig({

  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080/api',
        // changeOrigin: true,
        rewrite: path => path.replace(/^\/api/, '')
      }
    }
  },
  plugins: [vue(),VitePWA({
    registerType: 'autoUpdate',  
    manifest: {
      "name": "XXXX",
      "short_name": "XXXX",
      "start_url": ".",
      "display": "standalone",
      "background_color": "#fff",
      "description": "XXXX",
      "icons": [ {
        "src": "images/android-launchericon-48-48.png",
        "sizes": "48x48",
        "type": "image/png"
      },{
        "src": "images/android-launchericon-72-72.png",
        "sizes": "72x72",
        "type": "image/png"
      },{
        "src": "images/android-launchericon-144-144.png",
        "sizes": "144x144",
        "type": "image/png"
      },{
        "src": "images/android-launchericon-192-192.png",
        "sizes": "192x192",
        "type": "image/png"
      },{
        "src": "images/android-launchericon-512-512.png",
        "sizes": "512x512",
        "type": "image/png"
      }]
    },
    workbox: {
      // workbox options for generateSW
    }
  })]
})

```

## Vite 使用環境變數

設定 Vite 環境變數需要注意幾個事情

1. .env 可以使用 Vite 環境變數
  這邊提下小重點
  > .env                # 所有情況下都會加載
  > .env.local          # 所有情況下都會加載，但會被 git 忽略
  > .env.[mode]         # 只在指定模式下加載
  > .env.[mode].local   # 只在指定模式下加載，但會被 git 忽略

  這邊.env.local會被 git 忽略，後來發現是 `.local` 都會被 git 忽略。
  至於為什麼能被 git 忽略？因為建立 Vite 時候就已經被被在 `.gitignore`。~~我還以為 git 就有這個預設差點高潮~~

2. .env 環境變數需要用 `VITE_`開頭
```
DB_PASSWORD=foobar
VITE_SOME_KEY=123
```

使用變數也是很簡單，用 `import.meta.env.*` 就可以抓出環境變數
仔細一看，這個一般 js 也能使用

- [环境变量和模式 | Vite 官方中文文档](https://cn.vitejs.dev/guide/env-and-mode.html#env-files)
- [import.meta - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import.meta)
- [vue-cli 遷移 vite2 實踐小結 - SegmentFault 思否](https://segmentfault.com/a/1190000039371430)
### 選擇環境變數編譯

```bash
vite build --mode staging
```

假如 vite 沒用在全域變數時候，可以用`npm run build -- --mode test`，這樣就不用在`package.json`做修改調整
參考: [如何向npm腳本發送命令行參數？ - 問答 - 雲+社區 - 騰訊雲](https://cloud.tencent.com/developer/ask/50047)

這邊假如有指定 mode 時候，我這邊有做實驗，環境套用順序是這樣 `.env`->`.env.local`->`.env.test`


### 其他使用方式


- [【 Node.js 】利用 .env 與環境變數隱藏敏感資訊](https://learningsky.io/use-environmental-variables-to-hide-sensitive-information/)
- [vite.config.ts基础配置分享 - SKILL.NULL](https://skillnull.com/20200924/vite-config-ts%E5%9F%BA%E7%A1%80%E9%85%8D%E7%BD%AE%E5%88%86%E4%BA%AB/)
```js
// Dotenv 是一个零依赖的模块，它能将环境变量中的变量从 .env 文件加载到 process.env 中
const dotenv = require("dotenv")
 
const envFiles = [
  /** default file */ `.env`,
  /** mode file */ `.env.${process.env.NODE_ENV}`
]
 
for (const file of envFiles) {
  const envConfig = dotenv.parse(fs.readFileSync(file))
  for (const k in envConfig) {
    process.env[k] = envConfig[k]
  }
}
```

