---
title: JS export 用法筆記
date: 2021-06-14 14:31:51
tags: [javascript,export]
categories: JavaScript
---

之前有研究 export 要怎麼使用
不過後來沒特別記筆記
最近碰 Vite 稍微完一下 Vue3
發現有點忘記 export 怎麼用
稍微回顧一下，好險有想起來
[完全解析 JavaScript import、export | 卡斯伯 Blog - 前端，沒有極限](https://wcc723.github.io/development/2020/03/25/import-export/)
還是推薦看大神的文章
我這邊是給自己看的筆記



<!--more-->

## export default

這個我覺得最容易搞懂
首先，常常看到 js 前面都會寫 

`import xxx from 'xxx'`

>  這邊注意 export default 吃不到 以下方式
> `import {fn1,fn2} from 'xxx'`
> 這很重要，這很重要，這很重要，我講三遍(我應該很容易搞混)
> 下面會做詳細說明

相對我們的.vue檔案
會有一個設定
```javascript=
export default {
    fn1,
    fn2
}
```
這邊你會不會覺得放名子進去沒有做甚麼?
這邊其實用到

可以用另外一隻 js 呼叫 這隻 js
用
`import hello from 'xxx'`
PS:`hello.fn1()`,`hellofn2()` 

>  這邊注意 export default 吃不到 以下方式
> `import {fn1,fn2} from 'xxx'`
> 這很重要，這很重要，這很重要，我講三遍(我應該很容易搞混)
> 下面會做詳細說明
> 我寫這篇時候，一直以為可以

## export named

**這邊會有兩種形式寫法**

其實之前看，漏看到這段
看到很多套件沒有寫 export default
好像都是用 `export function aaa(){...}` 居多
但其實用法比較彈性

### export xxx named

這邊 xxx 可以換成 var,const, let, function 

就我上面的範例

```javascript=
const getReports = () => console.log('getReports')
export {getRerports}
```

```javascript=
import {getReports} from './fetch.js'
```

### export {}

[14 JavaScript ES6 - 物件縮寫object shorthand - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10206415)

```
export {}
```

這邊舉一個範例

```javascript=
//fetch.js
var bb = function (){
    console.log('hello')
}
export {bb}
//main.js 
import api from './fetch.js'; // 不能這樣取值

console.log(api)
//Uncaught SyntaxError: import not found: default

//main.js
import {bb} from './fetch.js';

console.log(bb)
// bb function 
```

## `export {}` 跟 `export xxx named` 重複變數會出錯嗎?(會)

其實到這邊我很好奇，`export {}` 跟 `export xxx named` 可以撞會出錯嗎
先說議結論**會**

```javascript=
// fetch.js
const c = {bb }

export const d = {bb }

export {bb,c,d}
// main.js
import api from './fetch.js';

console.log(api)
// Uncaught SyntaxError: duplicate export name 'd'
```

## export named 跟 export default 可共存?

先說結論，**可以**。(其實他們就是兩個不同用法，後面會說)

我目前嘗試使用 export named 和 export default 不衝突。

上面使用看看就知道

## export xxx named 和 export default 可共存?(重點)

先說結論，可以。(其實他們就是兩個不同用法，後面會說)
但 `import api from './fetch.js';`不是吃到 export named ，是吃**export default**
是吃**export default**
是吃**export default**
~~因為我以為 export named 也能吃...~~
因為我覺得很重要，所以講三遍(其實我覺得我會很容易忘掉)

```javascript=
//fetch.js
const c = {bb }

export const d = {bb }


export {bb,c}

export default {
    addReport
}

// main.js

import api from './fetch.js';

console.log(api)
// api.addReport
```

拿掉 export default 

```javascript=
//fetch.js
const c = {bb }

export const d = {bb }


export {bb,c}


// main.js

import api from './fetch.js';

console.log(api)
// Uncaught SyntaxError: import not found: default
```

## import * as named 和 import xxxx (重要)

~~剛開始我以為 * 是抓 export default~~
import * as named 是抓 `export named`
import xxx 是抓 `export default`

有找到文獻

[ModuleImport](https://esdiscuss.org/topic/moduleimport)

```javascript=
import * as fs from "fs"; // importing the named exports as an object
import Dict from "dict";  // importing a default export, same as ever

```

## 感想

我覺得 `export named` 和 `export default` 是不同的東西，這樣想就比較不會有問題
因為我把`export`來看，以為他們都是有關連的
實際操作，使用方式也不太一樣(import)
動手做做看就知道了

~~很多不是教學不說，其實是我自己腦補很多東西QQ~~

MDN文件可以看
[export - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)



