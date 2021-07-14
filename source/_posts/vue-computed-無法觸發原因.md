---
title: Vue computed 無法觸發原因
date: 2021-07-14 20:48:58
tags: [vue]
categories: Vue
---


> view 或者 methods 中有没有用到 pages，如果没有用到，那么是不会触发 computed 的

- [【VUE】当依赖属性改变时，computed为何没有被触发? - SegmentFault 思否](https://segmentfault.com/q/1010000020845026/)
- [那些關於 Vue 的小細節 - Computed 中 getter 和 setter 觸發的時間點 ~ PJCHENder 那些沒告訴你的小細節](https://pjchender.blogspot.com/2017/05/vue-computed-getter-setter.html)這篇詳細講了 getter 和 setter 實作，簡單來說 view 或 methods執行 沒有用到 computed 的屬性，就不會執行 getter 資料，所以就無法執行!

<!--more-->


因為這篇採雷了，也許我們要分析什麼時候用到 computed。我剛好用 monaco-editor ，此套件沒有用到 Vue 程式，所以沒辦法用到 data Binding 去做綁定，但是我用 Event 去觸發改 Vue 的值，也許應該要獨立把它包成 Vue 套件。目前 Vue3 測試階段，套件沒有支援 Vue3。

仔細回去看官網那篇文件[计算属性和侦听器 | Vue.js](https://v3.cn.vuejs.org/guide/computed.html)，真的是用在 View 的值，我當初以為 computed 裡面屬性裡面的值是因為偵測 function 裡面變數，但看來是我想太多，原理就是 getter。

這篇就記錄到這邊，因為這麼簡單原理，我卡了幾小時的關，不過最後也還債了...


小記:
想使用外部存js 元件有甚麼方法?
我這邊自己想的方向有:

1. 寫在 method 直接觸發 function
2. data binding

