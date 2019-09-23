---
title: 'CSS :fullscreen 全螢幕功能小記'
date: 2019-09-19 20:36:37
tags: [css,fullscreen]
categories: CSS
---

最近看到 CSS 可以控制全螢幕 style
簡單測試一下
還是有遇到一些小地雷
小記小記，防止下次彩雷

<!--more-->

## :fullscreen

是控制 CSS style ，不是控制畫面全螢幕，但這個對`F11`全螢幕沒有什麼作用

## JS 觸發全螢幕

```js
document.documentElement.requestFullscreen()
```

這邊注意使用CSS要用`:fullscreen` or `html:fullscreen`
使用`body:fullscreen`

## 地雷

### 開發者模式不能直接執行
在 console.log 實作不會有效果
會跑出錯誤
`TypeError: The expression cannot be converted to return the specified type.`

```js 
document.documentElement.requestFullscreen()
```

## 相關其他 JS 套件

[[前端軍火庫]screenfull.js - 全螢幕顯示就是這麼簡單 | 全端開發人員天梯 - 點部落](https://dotblogs.com.tw/wellwind/2016/12/20/front-end-screenfull-js)
[sindresorhus/screenfull.js: Simple wrapper for cross-browser usage of the JavaScript Fullscreen API](https://github.com/sindresorhus/screenfull.js)