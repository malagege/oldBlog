---
title: ECMAScript6 Module使用筆記
date: 2018-09-22 21:34:53
tags: [ecmascript6]
categories: JavaScript
---

最近因為學了VueJS，覺得事時候要進入Vue cli環境用用看
但知道Vue cli是使用Webpack
總覺得不了解JS模組化要怎麼寫
之後程式會踩到一堆雷
在此先記錄一些筆記

<!--more-->

在之前有看過nodejs的`require`，JavaScript的`import xxxx from ooo`
一直以為他們都是大同小意，可能可以互通
但今天看到這篇[Node中没搞明白require和import，你会被坑的很惨 - 腾讯Web前端 IMWeb 团队社区 | blog | 团队博客](http://imweb.io/topic/582293894067ce9726778be9)
{% asset_link 1.png "備份圖" %}
發現完全不一樣

由於我對COMMONJS、AMD跟nodejs不熟悉
所以我這篇指記錄ECMAScript 6 模組化

## (模組)導出 
```javascript
export var a = 1;
var b = 2;
export b;
export 1;//不能放不是變數，只能放function name跟object指定
```

詳細:[export - JavaScript | MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/export)

### export default
```javascript 
// 底下的 function 用 class, function* 也可以
export default expression;
export default function (…) { … } 
export default function name1(…) { … }
```
一個JS只能放一個export default

在寫模組導入的時候
在說明會比較清楚

基本上export會解析後面放的變數名稱

`export {xxxx, oooo,ffff}`
當export放物件(大陸翻:對象，最近看中文一直看不懂這詞XD)


```javascript
export { name1 as default, … };
export * from …;
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …;
```

> 另外要注意的是，如果想 import 的同時 export 出去，
> 並?不會 export 預設輸出。
```
export * from …;
```
> 你必須額外 import 它的預設輸出，然後再用 export
```
import mod from "mod";
export default mod;
```

## (模組)導入

如果我們要輸出單獨的 function, object, class 或當做 fallback 值來輸出的話，就可以用預設輸出：

```javascript
// module "my-module.js"
export default function cube(x) {
  return x * x * x;
}
export var b = 1;
```
外部檔案的 import 用法：
```javascript
import cube from 'my-module';
import {b} from 'my-module';
console.log(cube(3)); // 27
```
Note 注意預設輸出不能使用 var, let , const。

比較要注意的事情
`import`後面有沒有`{}`就不一樣了
`{}`裡面是放export 變數名稱
沒有的話，import xxx，xxx是指default導出的東西



## HTML載入
```html
<script type="module" src="a.js"></script>
```
現在Server都支援了ES6載入模組
當然要實驗看看


## 小小實驗
### HFS踩坑記，無法載入

在公司用hfs不能開啟，但用xxamp可以直行
但是在家裡hfs又可以用了...




### 兩個export物件會怎麼樣

這是我想的，好奇這樣能不能跑
```
export {xxxx,oooo}
export {zzzz,yyyy}
```


### b.js
```js
export {a,b}
export {c,d}
// export {a,c,d}
// SyntaxError: duplicate export name 'a'
```

詳細下載{% asset_link "jsModule.7z" 我寫的東西 %}


題外話
```
export {b}; // 这是ES6的写法，实际上就是{b:b}
```
原本以為文章寫錯
剛好邊看直播[[ Alex 宅幹嘛 ] 👨‍💻Vue 全家桶與 RESTful API 串接入門介紹 Part 2 - YouTube](https://www.youtube.com/watch?v=aYlihfn-Gmg)
有題到這個是ES6語法 XD