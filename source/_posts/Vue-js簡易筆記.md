---
title: "Vue.js簡易筆記"
date: 2018-09-19 23:22:14
tags: [vue]
categories: Vue
---

最近使用Vue.js寫幾個範例
先做個備忘錄

<!--more-->

{% asset_link vuetest.code 筆記連結 %}

了解完可以看下面的影片，可以更進一步了解vuejs
如物件、陣列、virtual DOM... 等等雷
[[Vue.js 台灣 Meetup] Style Guide 導讀 / Kuro Hsu - YouTube](https://www.youtube.com/watch?v=K8k5mkl-w4g)
EventBus的雷，刪除component記得做回收動作
[[ Alex 宅幹嘛 ] 👨‍💻Vue EventBus 使用操作與外掛開發 - YouTube](https://www.youtube.com/watch?v=eFtw__i7vOk)
還算滿好懂的過場教學
[Vue.js (14) - 過場效果及動畫 | Tony Blog](http://blog.tonycube.com/2017/06/vuejs-14-transition-animation.html?m=1)
{% asset_link 2.png "備份圖" %}
有講sync跟emit的差別，其實原理都一樣，但是sync是寫死的，不用多寫東西
[【Vue】Vue中的父子组件通讯以及使用sync同步父子组件数据 - 外婆的彭湖湾 - 博客园](http://www.cnblogs.com/penghuwan/p/7473375.html)
{% asset_link 1.png "備份圖" %}

live server跟json server的reload坑 (NOTE:liveServer.setttings.ignoreFiles)
https://youtu.be/MCTETw0Slrw?t=1h21m51s


vue cli electron 
[remote | Electron 中文文档](https://wizardforcel.gitbooks.io/electron-doc/content/api/remote.html)
[nklayman/vue-cli-plugin-electron-builder: A Vue Cli 3 plugin for Electron with no required configuration](https://github.com/nklayman/vue-cli-plugin-electron-builder)
[remote | Electron](https://electronjs.org/docs/api/remote#remotegetcurrentwindow)