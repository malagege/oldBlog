---
title: Vue 動畫過渡(Transition) 筆記
date: 2021-10-09 23:20:30
tags: [vue]
categories: Vue 
---

Vue 動畫過渡(Transition) 筆記

<!--more-->

## WEB 動畫知識

一個是 Transition，另一個是 Animation。

## Transition / Animation 釐清差異

可參考
- [Day27：小事之 Transition 與 Animation - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10197303)

![](https://i.imgur.com/hu1wTEp.png)


## Vue 的規範

![](https://v3.cn.vuejs.org/images/transitions.svg)

Enter
畫面顯示(如 v-if 出來)

1. xxx-enter-from, xxx-enter-active
2. xxx-enter-active
3. xxx-enter-to, xxx-enter-active
4. no(Vue-transition) class

Leave
畫面移除(如 v-if 移除)

1. xxx-leave-from, xxx-leave-active
2. xxx-leave-active
3. xxx-leave-to, xxx-leave-active
4. no(Vue-transition) class

### xxx怎麼命名

透過html
```htmlembedded=
<transition name="bounce">
```

- bounce-leave-from
- bounce-leave-active
- bounce-leave-to
- bounce-leave-active

## 一般使用

## 搭配 Vue 過渡 (Enter/Leave & List Transitions)

```htmlembedded=
 <transition name="bounce">
     <p v-if="show">
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris facilisis
      enim libero, at lacinia diam fermentum id. Pellentesque habitant morbi
      tristique senectus et netus.
    </p>
 </transition>
```


```css=
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}
```

小記重點
1. `<transition>`的 name(xxx)
2. 設定 xxx-leave-from, xxx-leave-active, xxx-enter-to 過渡屬性


## 搭配 Vue 過渡 (Enter/Leave & List Transitions) class

- enter-from-class
- enter-active-class
- enter-to-class
- leave-from-class
- leave-active-class
- leave-to-class


### 使用 animate class

```htmlembedded=
<link
  href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.0/animate.min.css"
  rel="stylesheet"
  type="text/css"
/>

<div id="demo">
  <button @click="show = !show">
    Toggle render
  </button>

  <transition
    name="custom-classes-transition"
    enter-active-class="animate__animated animate__tada"
    leave-active-class="animate__animated animate__bounceOutRight"
  >
    <p v-if="show">hello</p>
  </transition>
</div>

```

簡單重點，不需考慮 form/to 概念`。

1. name="custom-classes-transition"
2. enter-active-class="animate__animated animate__tada"
3. leave-active-class="animate__animated animate__bounceOutRight"

### 回憶第一步驟 name 做甚麼?

> 對於這些在過渡中切換的類名來說，如果你使用一個沒有名字的 `<transition>`，則 v- 是這些class名的默認前綴。如果你使用了 `<transition name="my-transition">`，那麼 v-enter-from會替換為 my-transition-enter-from。

上面`xxx怎麼命名`有提到，感覺很容易忘記是這樣命名。

## 搭配 Animate.css

可以看[【Day 26】CSS Animation - CSS 動畫資源蒐集與使用教學 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10251903)

### 官網建議

1. 不要使用 root Element (html,body)
2. 不要使用大的 Element 做動畫 (Bad UX)
3. 不要使用無限動畫(分心使用者注意力)
4. Mind the initial and final state of your elements(animation-fill-mode)
5. 不能用在inline(要使用 inline-block)


### 滾動到畫面觸發動畫

[How to trigger a CSS animation on scroll](https://coolcssanimation.com/how-to-trigger-a-css-animation-on-scroll/)

IntersectionObserver
```
const observer = new IntersectionObserver(entries => {
  // Loop over the entries
  entries.forEach(entry => {
    // If the element is visible
    if (entry.isIntersecting) {
      // Add the animation class
      entry.target.classList.add('square-animation');
    }
  });
});

observer.observe(document.querySelector('.square'));
```

這邊題外話，之前有看過 Observer，這邊也有看到[vue-chat-scroll和MutationObserver小記 | 程式狂想筆記](https://malagege.github.io/blog/2018/10/24/vue-chat-scroll%E5%92%8CMutationObserver%E5%B0%8F%E8%A8%98/)，雖然兩者沒關係，可以看更多 Observer 在[Web APIs | MDN](https://developer.mozilla.org/zh-TW/docs/Web/API)搜尋 Observer`。

### 彩蛋

我猜我應該沒有空整理這個，先貼上來吧。

[認識 Intersection Observer API：實作 Lazy Loading 和 Infinite Scroll | by Mike Huang | 麥克的半路出家筆記 | Medium](https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E8%AA%8D%E8%AD%98-intersection-observer-api-%E5%AF%A6%E4%BD%9C-lazy-loading-%E5%92%8C-infinite-scroll-c8d434ad218c)


沒想到 lazy load 圖片，還能做到 Infinite Scroll 。這麼強大。


[WeakMap and WeakSet（弱映射和弱集合）](https://zh.javascript.info/weakmap-weakset)