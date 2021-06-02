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