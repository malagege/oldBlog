---
title: Vue3 無法正確 Render 更新畫面
date: 2022-02-13 23:58:23
tags: [vue,render]
categories: Vue
---

最近遇到很奇怪問題， 由於Vue Component 我把Timer 拆出去 js 做，但 prop 帶進去參數，後面更改參數沒有做更新。這讓我想到 Vue 2 也有類似這個問題，所以進一步探討發現是跟`Reactivity in Depth`有關。


[Reactivity in Depth | Vue.js](https://v3.vuejs.org/guide/reactivity.html)

<!--more-->

## Vue 2 Reactivity 

網路上還滿多 Vue 2 Reactivity in Depth 文章，但是這個跟 Vue 3 問題沒關係，所以解決方法無效。

[[掘竅] 為什麼畫面沒有隨資料更新 - Vue 響應式原理（Reactivity） ~ PJCHENder\<br>那些沒告訴你的小細節](https://pjchender.blogspot.com/2017/05/vue-vue-reactivity.html)


## 基於 Proxy

Vue 3 使用到 Proxy 物件做資料更新，相關網路上爬文一堆。

```javascript=
var handler = {
    get: function(target, name) {
        return name in target ?
            target[name] :
            37;
    }
};

var p = new Proxy({}, handler);
p.a = 1;
p.b = undefined;

console.log(p.a, p.b); // 1, undefined
console.log('c' in p, p.c); // false, 37
```


這邊簡單說明，主要看到 p 沒有這個屬性，因為是 proxy 關係，所以 console.log 印出來只會看到 proxy，可以看到 `_target` 看到內容，可使用`JSON.parse(JSON.strinfy())`大法，當然`Object.assign()`也可以用出來。

參考MDN 範例: [Proxy - JavaScript | MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Proxy)


## 解法

### 帶 Proxy 參數

竟然知道原理了，把 Proxy 值帶進去做更新，多放一個參數，js 修改 Vue component的 data 使用帶進去 proxy 確實可以解決。


[修正時間無法 render 問題(Reactivity) · malagege/vue-electron-Pomodoro@4103a3f · GitHub](https://github.com/malagege/vue-electron-Pomodoro/commit/4103a3f3033b909106144979da46c1c2f0023dcb)

這算 workaound??感覺這樣寫不好看，不過改的幅度不大。

### 寫成 Vue Component

就是寫成 Vue 元件，但這樣感覺不能套用到一般專案，使用參數帶入也只能從 html 那邊帶入，感覺無法從 js 那邊下手。


## 其他整理

reactive 


[Vue | 認識 ref 與 reactive 基本運作 - James Pan - Medium](https://jamespan62537.medium.com/vue-%E8%AA%8D%E8%AD%98-ref-%E8%88%87-reactive-%E5%9F%BA%E6%9C%AC%E9%81%8B%E4%BD%9C-756cbc416584)


[reactivie 整体概览 | Vue3](https://vue3js.cn/reactivity/)