---
title: vuejs hook和router hook小記
date: 2018-09-30 17:14:16
tags: [vue]
categories: JavaScript
---

vuejs hook和router hook小記

<!--more-->

{% asset_link vue_c_test_hook.zip 檔案 %}

心得/紀錄:

Vue hook
1. beforeCreate不會抓到this屬性(props,data)，原因還沒被做出來

2. component剛建立出來，不會觸發到beforeUpdate,updated，只有template被更新的時候會觸發，但是!!!改data...等等沒觸發到template也不會有問題

Vue Router hook

1. 進行第2次寫入，不會有效果，一樣保持第一次beforeEach hook東西，也許這跟event沒有關係，
```js
    //測試router hook
    router.beforeEach((to,from,next)=>{
        console.log('1');
    });

<!-- 進行第2次寫入，不會有效果，一樣保持第一次beforeEach hook東西
也沒有看到文件說明 -->
router.beforeEach((to,from,next)=>{
    console.log('2');
    console.log(to);
    console.log(from);
    console.log(next);
});
```

2. Vue scrollBehavior 滚动行为，詳細看範例，這篇不好讀
 [Vue scrollBehavior 滚动行为 - 悦涵 - 博客园](https://www.cnblogs.com/sophie_wang/p/7880261.html)

3. ~~全域router hook的`this`，應該是指router變數~~結果...我console.log出來是`underfined`

子組件 hook

1. 
```js
  beforeRouteEnter (to, from, next) {
    // 在渲染該組件的對應路由被 confirm 前調用
    // 不！能！獲取組件實例 `this`
    // 因為當守衛執行前，組件實例還沒被創建
  },
  beforeRouteUpdate (to, from, next) {
    // 在當前路由改變，但是該組件被覆用時調用
    // 舉例來說，對於一個帶有動態參數的路徑 /foo/:id，在 /foo/1 和 /foo/2 之間跳轉的時候，
    // 由於會渲染同樣的 Foo 組件，因此組件實例會被覆用。而這個鉤子就會在這個情況下被調用。
    // 可以訪問組件實例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 導航離開該組件的對應路由時調用
    // 可以訪問組件實例 `this`
  }
```

meta 這方面就看官網範例，可以看一下範例註解


順便一提，css有一個[scroll-behavior - CSS: Cascading Style Sheets | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-behavior)
但是跟vue沒關係

[理解 $nextTick 的作用](https://zhuanlan.zhihu.com/p/26724001)還沒有研究，剩下就是vuex

參考來源:
[javascript - vue-router钩子beforeRouteEnter函数获取到this实例 - SegmentFault 思否](https://segmentfault.com/q/1010000008973485)
[路由元信息 | Vue Router](https://router.vuejs.org/zh/guide/advanced/meta.html)
[导航守卫 | Vue Router](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html#%E5%AE%8C%E6%95%B4%E7%9A%84%E5%AF%BC%E8%88%AA%E8%A7%A3%E6%9E%90%E6%B5%81%E7%A8%8B)
[scroll-behavior - CSS: Cascading Style Sheets | MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/scroll-behavior)
[Vue scrollBehavior 滚动行为 - 悦涵 - 博客园](https://www.cnblogs.com/sophie_wang/p/7880261.html)
[理解 $nextTick 的作用](https://zhuanlan.zhihu.com/p/26724001)
[vue2学习篇一 vue-router 导航钩子 丨 智杰之家 - 关注互联网开发](https://www.zhi-jie.net/vue2-study-vue-router-navigation-hook/)