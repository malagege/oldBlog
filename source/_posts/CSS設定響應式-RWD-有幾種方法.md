---
title: CSS設定響應式(RWD)有幾種方法
date: 2019-05-05 18:33:31
tags: [css, RWD, media]
categories: CSS
---

最近看 SASS 有設定@media 方法
再仔細查了一下，還有別種寫法
有看到一些自己不會這樣寫的
特別筆記一下

<!--more-->

## 一般常見寫法

一般常見這種寫法
`@media screen and (min-width: 400px) and (max-width: 700px)`

這種寫法順序會比較安全

```css
@media screen and (min-width: 400px) and (max-width: 700px) {
  .header {
  }
}
```

## 特別寫法

在[使用 sass mixin 來開發 responsive 網站 | Hello Santa web design,drupal and more](https://blog.hellosanta.com.tw/%E7%B6%B2%E7%AB%99%E8%A8%AD%E8%A8%88/%E5%89%8D%E7%AB%AF/%E4%BD%BF%E7%94%A8sass-mixin%E4%BE%86%E9%96%8B%E7%99%BCresponsive%E7%B6%B2%E7%AB%99)裡面有看到這種寫法

```css
.div{
         //desktop
         @media all and (min-width: 960px) {
                padding:20px;
          }
         //pad
         @media all and (min-width: 760px) and (max-width: 959px) {
              padding:10px;
         }
         //mini pad
         @media all and (min-width: 480px) and (max-width: 759px) {
               padding:5px;
         }
         //mobile
         @media all and (max-width: 480px) {
               padding:0px;
         }
      }
}
```

沒想到 media 可以放在裡面
這樣寫也比較不會亂

## 簡單寫法(需要小心順序)

```css
.header {
  padding: 20px;
}
//pad
@media (max-width: 959px) {
  .header {
    padding: 10px;
  }
}
//mini pad
@media (max-width: 759px) {
  .header {
    padding: 5px;
  }
}
//mobile
@media (max-width: 480px) {
  .header {
    padding: 0px;
  }
}
```

> 建議是只寫 max-width 的話由大像素寫到小像素，min-width 按小像素到大像素的順序，從上往下寫，不然會達不到控制的樣式目的。

這種寫法可能需要注意順序，可參考[@media screen 的書寫順序 - WEB 前端娃——steiny - CSDN 博客](https://blog.csdn.net/cmlddcml/article/details/48973589)

## SASS 寫法

用 mixin 方法，但這邊使用上面簡單方法設定

```sass
// device
$mobile: 568px;
$pad: 768px;
$desktop: 960px;

// color
$red: #ff0000;
$black: #000;

@mixin mobile {
    @media (max-width: $mobile) {
        @content;
    }
}

@mixin pad {
    @media (max-width: $pad) {
        @content;
    }
}

@mixin desktop {
    @media (max-width: $desktop) {
        @content;
    }
}



.header{
    color: $black;
    @include pad{
        color: $red;
    }
    @include mobile{
        display: none;
    }
}

```

## 深入了解

### @media max-width 和 min-width 順序問題

深入了解[@media screen 的書寫順序 - WEB 前端娃——steiny - CSDN 博客](https://blog.csdn.net/cmlddcml/article/details/48973589)

@media (max-width: 959px) //裝置小於 959 會進來
@media (max-width: 759px) //裝置小於 759 會進來
@media (max-width: 480px) //裝置小於 480 會進來

但是寫反會遇到什麼事情

裝置 300 因為 CSS 順序關係，所以會吃到 959 的設定
@media (max-width: 480px) //裝置小於 480 會進來
@media (max-width: 759px) //裝置小於 759 會進來
@media (max-width: 959px) //裝置小於 959 會進來

[@media screen 的書寫順序 - WEB 前端娃——steiny - CSDN 博客](https://blog.csdn.net/cmlddcml/article/details/48973589)
簡單口決真的記得

> 建議是只寫 max-width 的話由大像素寫到小像素，min-width 按小像素到大像素的順序，從上往下寫，不然會達不到控制的樣式目的。

### @media 的 screen 可以省略

為什麼我看有些 code 裡面的@media 可以省略?
翻了[@media - CSS | MDN](https://developer.mozilla.org/zh-TW/docs/Web/CSS/@media#media-type)沒有寫
不過找到[前端新手村 Media Query - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10194937)
裡面有提

> media type 是 all 時，可以省略。

不過我之前專案有用到@media print 功能
只要用 CSS 就能調整列印樣式
真的是很方便

[Print —— 被埋没的 Media Type – 腾讯 CDC](https://cdc.tencent.com/2014/08/19/print-%E8%A2%AB%E5%9F%8B%E6%B2%A1%E7%9A%84media-type/)

### 有關於 compass

當出學 SASS 的時候，有看到 compass
但最近感覺很少人提到 compass
所以我有查了一下
原來 compass 已經很久沒更新了

Compass 有哪些好處呢?裡面[[scss]Compass | hannahpun](https://www.hannahpun.me/compass/)裡面 2 個我覺得不錯

> 1. Sprite
> 2. Helper 函數( image-width,image-height 太神!!!)

取代 compass 我查到用 gulp 或 webpack 做 sprite、CSS3 各瀏覽器的 CSS 可以通過 postcss 解決

- [Compass 替代方案(1) - 更快速的 Sass | 卡斯伯 Blog - 前端，沒有極限](https://wcc723.github.io/sass/2015/12/20/replace-compass-with-node-sass/?utm_source=tuicool&utm_medium=referral)
- [Compass 替代方案(2) - 透過 PostCSS，停止加入不必要的 prefix | 卡斯伯 Blog - 前端，沒有極限](https://wcc723.github.io/sass/2015/12/25/replace-compass-with-node-sass-2/)
- [Compass 替代方案(3) - 加入 SVG Sprites | 卡斯伯 Blog - 前端，沒有極限](https://wcc723.github.io/sass/2016/01/03/replace-compass-with-node-sass-3/)

上面有機會用的話，再來詳細筆記

PS: 當然 image-width,image-height 目前沒有解決方案

參考來源：

- [Mobile Web 前端技術筆記(二): Media Queries 與 CSS – 烏托比亞](https://hsinyu00.wordpress.com/2011/04/05/mobile-web-media-queries/)
- [@media screen 的书写顺序 - WEB 前端娃——steiny - CSDN 博客](https://blog.csdn.net/cmlddcml/article/details/48973589)
- [使用 sass mixin 來開發 responsive 網站 | Hello Santa web design,drupal and more](https://blog.hellosanta.com.tw/%E7%B6%B2%E7%AB%99%E8%A8%AD%E8%A8%88/%E5%89%8D%E7%AB%AF/%E4%BD%BF%E7%94%A8sass-mixin%E4%BE%86%E9%96%8B%E7%99%BCresponsive%E7%B6%B2%E7%AB%99)
- [CSS Media Queries 詳細介紹 - OXXO.STUDIO](https://www.oxxostudio.tw/articles/201810/css-media-queries.html)
- [[scss]Compass | hannahpun](https://www.hannahpun.me/compass/)
- [Compass 替代方案(1) - 更快速的 Sass | 卡斯伯 Blog - 前端，沒有極限](https://wcc723.github.io/sass/2015/12/20/replace-compass-with-node-sass/?utm_source=tuicool&utm_medium=referral)
- [Sass 的@import @mixin @content @extend 與@function « StevenTTuD's notes](http://steventtud.logdown.com/posts/2015/06/01/279546)
- [Sass 教學 (11) - @content - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10156850)
