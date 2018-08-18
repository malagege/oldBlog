---
title: 'CSS position:absolute 垂直置中妙用'
date: 2018-08-15 22:50:48
tags: [css,position,absolute]
categories: CSS
---

最近發現HTML tag `dialog`屬性可以用了
當下好奇怎麼可以使用??
當下好奇瀏覽器怎麼排排板的
發現原來是用absolute日上margin可以這樣置中

<!--more-->


PS: 目前Firefox 53裡的`about:config`需要設定`dom.dialog_element.enabled`為true
才能完整用js操作
[HTMLDialogElement.returnValue - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/HTMLDialogElement/returnValue)
https://segmentfault.com/a/1190000003820437#articleHeader9


[html - Why does "position: absolute; left: 0; right: 0; width: XYpx; margin: 0 auto" actually center? - Stack Overflow](https://stackoverflow.com/questions/29261595/why-does-position-absolute-left-0-right-0-width-xypx-margin-0-auto-ac)
[css - div的position:absolute；left:0;right:0;margin:0 auto;为什么可以居中？ - SegmentFault 思否](https://segmentfault.com/q/1010000008325121)
[CSS规范 > 10 视觉格式化模型详述 Visual Formatting Model Details - HaoyCn's Blog - SegmentFault 思否](https://segmentfault.com/a/1190000003820437#articleHeader9)
```css
postition:absolute; 
left:0px;
right:0px;
```
要加`margin auto;`

但為什麼這樣就能置中呢?
網路找了一下，其實滿不好找的(但還是有的)...
[CSS 垂直置中的七個方法 - OXXO.STUDIO](http://www.oxxostudio.tw/articles/201502/css-vertical-align-7methods.html)

不過...為什麼這樣能置中?我就Google找了一下中文文件(英文太菜了>.<)

這篇有講到[css - 絕對定位div寬度如何自適應內容。 - SegmentFault 思否](https://segmentfault.com/q/1010000010907551)


### containing block
> 怎么确定一个元素的containing block，由position属性确定：
> 1. static(默认的)/relative：简单说就是它的父元素的内容框（即去掉padding的部分）
> 2. absolute: 向上找最近的定位为absolute/relative的元素
> 3. fixed: 它的containing block一律为根元素(html/body)，根元素也是initial containing block
[CSS 中 block-level boxes、containing block、block formatting context 三者之间的区别和联系是怎样的？ - 知乎](https://www.zhihu.com/question/20086234)

難怪之前會覺得position設定width height百分比怪怪的，看到這個終於解開我心中疑惑


## inline 裡面element使用position位置
[Containing Block(包含块模型) - 简书](https://www.jianshu.com/p/c67a7c1cbb00)
[CSS direction属性简介与实际应用 « 张鑫旭-鑫空间-鑫生活](http://www.zhangxinxu.com/wordpress/2016/03/css-direction-introduction-apply/)
  

[CSS规范 > 10 视觉格式化模型详述 Visual Formatting Model Details - HaoyCn's Blog - SegmentFault 思否](https://segmentfault.com/a/1190000003820437#articleHeader9)
那麼該收縮適應寬度即：min(max(首選最小寬度,可用寬度),首選寬度)。
> 3個寬度說明：
    首選寬度：按照不自動斷行的方式排版，取得的內容寬度（可以理解為不受容器限制自動排列的寬度就是你想要的效果）
    首選最小寬度：calculate the preferred minimum width, e.g.,by trying all possible line breaks（我理解就是在1的情況下你把容器不斷縮小到不能換行，一般是單個文字的寬度）
    可用寬度：就是把right設置為0，計算出的width

## 更多詳細要了解的
(未深入了解的，吸收不能>.<，之後踩雷再研究)
[深入理解CSS绝对定位absolute - 小火柴的蓝色理想 - 博客园](http://www.cnblogs.com/xiaohuochai/p/5312917.html)
裡面有一些觀念，
1.position:absolute遇到float:right，浮動(float)就會失效。但是relative就沒有這個問題
2.position:absolute，parent並不會算到子原素absolute的寬度、高度
還有很多

[CSS绝对定位的应用 - 小火柴的蓝色理想 - 博客园](http://www.cnblogs.com/xiaohuochai/p/5315942.html)
左半邊翻圖範例很有意思

[CSS进阶——绝对定位元素的宽高是如何定义的 - 掘金](https://juejin.im/post/5935142e2f301e006b0a7b55)
>根元素（在HTML文档中即<html>元素）所在的包含块即是初始包含块，对于浏览器来说：初始包含块的大小即是视口的大
>
這個雷，我還真的不知道...
想說怎麼不是在最底端呢....


[由布局学习CSS——从两列布局来看position：absolute的用法 - shawnXiao - 博客园](http://www.cnblogs.com/sc-xx/archive/2012/03/28/2422371.html)

position:absolute

## 隨意寫的範例loading
https://jsbin.com/diyavuyihi/1/edit?html,css,output

