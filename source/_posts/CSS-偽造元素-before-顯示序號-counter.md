---
title: 'CSS 偽造元素(::before)顯示序號(counter)'
date: 2021-10-09 23:22:41
tags: [css]
categories: CSS
---

# CSS 偽造元素(::before)顯示序號(counter)

- [重新認識 CSS - Pseudo-element (偽元素) | Titangene Blog](https://titangene.github.io/article/css-selector-pseudo-element.html)

- [Custom List Number Styling | CSS-Tricks](https://css-tricks.com/custom-list-number-styling/)

- [[CSS 年終特賣] 讓 CSS 自動幫你數數](https://blog.hinablue.me/use-css-counter-to-make-your-own-toc/)

<!--more-->

## 簡單範例

```css=
zxx-grid zxx-item::before{
  content: "0" counter(section);
  font-weight: bold;
  font-size: 3rem;
  line-height: 1;
  position: absolute;
  right: 0px;
  bottom: 0px;
}

zxx-grid zxx-item{
  position: relative;
  counter-increment: section;
}
```



## 簡單說明

重點整理:
1. `counter-reset:num;` 放在父層
2. `counter-increment: num;`、`content: counter(num) ".";`放在子層，
  
https://jsbin.com/yebulahabu/edit?html,css,output



## 網路文章

[CSS 偽元素 ( content 與 counter ) - OXXO.STUDIO](https://www.oxxostudio.tw/articles/201706/pseudo-element-2.html)