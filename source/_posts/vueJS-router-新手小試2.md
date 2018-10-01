---
title: vueJS router 新手小試2
date: 2018-09-27 21:05:51
tags: [vue]
categories: JavaScript
---

補充上一篇東西
這次有加入了分頁功能

<!--more-->

{% asset_link code.zip "範例程式" %}

心得/開發問題

1. transition-group mode沒有效果，transition-group有v-move移轉特效
網路上有找到用transition-delay來解決問題
[Allow mode for transition-group · Issue #3974 · vuejs/vue · GitHub](https://github.com/vuejs/vue/issues/3974)

2. 上一篇data binding雙向綁定問題，可以使用`:value`做綁定
但是發現radio會失效，雖然可以使用name方式解決，但是感覺不太滿意
最後想到用`JSON.parse(JSON.stringify(xxx))`解決
~~但放到`mounted`從`/edit/:id`進來吃不到this.data~~
最後想到用computed解決
(PS:好像是template出錯，有嘗試著用v-if閃過，但我覺得還是`computed`解比較漂亮)
2018/09/28 重新實作[JS Bin - Collaborative JavaScript Debugging](http://jsbin.com/siciwelobo/1/edit?html,js,output)
功能有正常出來


3. 最後新增資料/修改資料，回去首頁的時候，資料還是舊的！我以為是拿出跟之前畫面一樣
但我發現我錯了，簡單做個小實驗
發現router 回去component物件都還會還原，但是`data`資料不會

{% asset_img 1.gif title %}

但是...是vue-router問題，還是component也會這樣

所以，還是實驗一下到底發生甚麼事情

`參考vue_c_test.html`

vuerouter跟一般component一樣
會重新產出

但是我的回首頁狀態還在
到底發生甚麼事情
**後來突然想到會不會父組件傳data給子組件**
所以才會造成資料不會重新整理

**router 踩雷小記**
router 載入compoent是分開的!!!這個好雷
[Using a component name in a route definition · Issue #77 · vuejs/vue-router](https://github.com/vuejs/vue-router/issues/77)
```js
    var router = new VueRouter({
        routes: [
  { path: '/a', component: Vue.component('a-component') },
  //router 載入compoent是分開的!!!這個好雷
  //[Using a component name in a route definition · Issue #77 · vuejs/vue-router](https://github.com/vuejs/vue-router/issues/77)
  { path: '/b', component: BComponent }
]
    });
```


4. `res.headers.get("X-Total-Count");`得出來是數字，放到component可能要注意轉型，`parseInt(xxx)`
```
[Vue warn]: Invalid prop: type check failed for prop "records". Expected Number, got String.

found in
```


[vue2学习篇一 vue-router 导航钩子 丨 智杰之家 - 关注互联网开发](https://www.zhi-jie.net/vue2-study-vue-router-navigation-hook/)
router hook我還沒有玩到
看來我覺得還會有vueJS-router 新手小記3