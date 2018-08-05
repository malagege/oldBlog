---
title: '[HTML]<p>(Phrasing元素)放入div或block元素會分割成兩塊'
date: 2018-06-24 13:31:00
tags: [HTML,p,phrasing]
categories: HTML
---

最近剛好網路爬文看到這個

不過...，我相信不知道這個很容易踩雷
就先記錄一下

<!--more-->

>Phrasing
所有可以放在p标签内，构成段落内容的元素均属于Phrasing元素。因此，所有Phrasing元素均属于Flow元素。在HTML5标准文档中，关于Phrasing元素的原始定义为：
    Phrasing content is the text of the document, as well as elements that mark up that text at the intra-paragraph level. Runs of phrasing content form paragraphs.
对于这一定义，个人认为不应当使用“text”这一容易引起误解的词，事实上，一个元素即使不是文本，只要能包含在p标签中成为段落内容的一部分，就可以称之为Phrasing元素。比如：audio、video、img、select、input等元素(经测试，这些元素都可以放置在p标签中)。一个不太精确的类比是：HTML5中的Phrasing元素大致就是HTML4中所定义的inline元素。
Phrasing元素内部一般只能包含别的Phrasing元素。


簡單講就是p元素裡面不能隨意插進一些元素

```html
Phrasing content defines the text and the mark-up it contains. Runs of phrasing content make up paragraphs.
Elements belonging to this category are <abbr>, <audio>, <b>, <bdo>, <br>, <button>, <canvas>, <cite>, <code>, <command>, <data>, <datalist>, <dfn>, <em>, <embed>, <i>, <iframe>, <img>, <input>, <kbd>, <keygen>, <label>, <mark>, <math>, <meter>, <noscript>, <object>, <output>, <progress>, <q>, <ruby>, <samp>, <script>, <select>, <small>, <span>, <strong>, <sub>, <sup>, <svg>, <textarea>, <time>, <var>, <video>, <wbr> and plain text (not only consisting of white spaces characters).
```

看開發工具，除了排版變怪了，你會看到開發工具有兩個`<p></p>`，最後一個格式還跑掉

{% asset_img show1.png p範例split %}

[HTML Standard](https://html.spec.whatwg.org/multipage/grouping-content.html#the-p-element)
[W3C HTML5标准阅读笔记 – 元素分类与内容模型(Content Model)](http://blog.shaochuancs.com/w3c-html5-content-model/)