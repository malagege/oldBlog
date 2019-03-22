---
title: 我的JavaScript筆記
date: 2019-03-10 21:59:18
tags:
categories:
---

由於今天上課
簡單小記今天上課我覺得記不住，但是該紀錄的筆記
有可能會記入錯誤
所以算是我的觀點
有錯不要怪我喔!!有錯不要怪我喔!!有錯不要怪我喔!!
想真的了解 JS，可以自己 Google 一下核心部分課程
應該也只有一個人教(就不打廣告了)
這邊是我的筆記(我覺得重要的)

<!--more-->

變數是指標，指向記憶體中的資料

宣告變數`var`的同時，也會成為某物件下的屬性 (不一定是根物件,ex:function)
屬性可以用 delete 刪除，`變數**無法**被刪除

var 在一般外面 function 宣告，會寫在 window
function 寫在 ao 物件[ECMA-262 Javascript 核心](http://notepad.yehyeh.net/Content/WebDesign/Javascript/ECMA/Core/JavaScriptCore.php#section7)

```js
Number.prototype.name = "Hello";
var a = 1;

console.log(a.name); // 'Hello' number 祖先關係，原型

delete a.name;
// ture 刪一個不存在地方，會回傳 true \*這一點真的會讓人家以為有刪除成功

a.name;
//Hello
```

var 與 let 的範圍

JavaScript 裡的型別

原始型別

- number
- string
- boolean
- null
- undefined
- symbol
- 物件型別

原始型別

屬性不可添加(但可以擺繼承 interface[string,number...])

物件型別
可以擴充屬性

這邊也有提到重點[前端工程研究：關於 JavaScript 中物件的 valueOf 方法 | The Will Will Web](https://blog.miniasp.com/post/2013/07/11/Front-end-Research-JavaScript-valueOf-method)
注意 sort 排序竟然是依照 toString 去做(我還沒有遇到這個雷...)
如何判斷數字排序呢?
[Array.prototype.sort() - JavaScript | MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

```js
var numbers = [4, 2, 5, 1, 3];
numbers.sort(function(a, b) {
  return a - b;
});
```

小記 sort

```
* 若 compareFunction(a, b) 的回傳值小於 0，則會把 a 排在小於 b 之索引的位置，即 a 排在 b 前面。
* 若 compareFunction(a, b) 回傳 0，則 a 與 b 皆不會改變彼此的順序，但會與其他全部的元素比較來排序。備註：ECMAscript 標準並不保證這個行為，因此不是所有瀏覽器（如 Mozilla 版本在 2003 以前）都遵守此行為。
* 若 compareFunction(a, b) 的回傳值大於 0，則會把 b 排在小於 a 之索引的位置，即 b 排在 a 前面。
* compareFunction(a, b) 在給予一組特定元素 a 及 b 為此函數之兩引數時必須總是回傳相同的值。若回傳值不一致，排序順序則為 undefined。(看不懂這還意義，但好像undefined會移到最後面去)
[sorting - javascript array.sort with undefined values - Stack Overflow](https://stackoverflow.com/questions/4783242/javascript-array-sort-with-undefined-values)
```

簡單來說 `a,b`回傳小於 0 的時候，不會調整；反之，`a,b`回傳大於 0 的時候，會調整位置成`b,a`

原始型別包裹物件
**需要用 new(沒加 new 是轉型)**
包裹物件可以改裡面屬性

```js
var numObj = 17;
numObj.xxx = "2";
console.log(numObj); // undefined
```

```js
var numObj = new Number("17");
numObj.xxx = "2";
console.log(numObj); // 2
```

```js
var s = "Hello";
s[0] = "J";
console.log();
```

Number VS parseInt()

Number(1)是轉型，這跟 parseInt 很像，但細部內容不一樣

```js
Number("1abc"); // NaN
parseInt("1abc", 10); // 1
```

之前公司都是用 parseInt(xxx)轉型，公司的老鳥有說過後面要加第二個參數比較好`parseInt(xxx,10)`
但是轉型的話還是用 Number 比較好

數字運算需要注意的事情

0.1 + 0.2 != 0.3 => true

(0.5).toString(2); // 0.1
(0.25).toString(2); //0.01
(0.125).toString(2); //0.001

0.300000000000004

(0.1).toString(2)
// 0.0001100110011001100110011001100110011001100110011001101
(0.2).toString(2)
// 0.001100110011001100110011001100110011001100110011001101

所以正常要做大數字的運算還是需要用到別的寫好的 js

BigInt
目前只有 chrome 支援

NULL 要注意的事情
`typeof null // object`

null 只能跟 null 與 undefined 比較
NaN 跟任何物件比較都是 false

所以平常`typeof a`這段沒辦法知道 a 變數是不是物件(因為也有可能是 null)

`typeof(a) == 'object' && a!== null`
完整判斷寫法

undefined IE8 的 bug

```js
var undefined = 33;
console.log(window.undefined);
var a; // 33 (chrome,firefox,IE>=9是undefined)
```

jQuery 處理 undefined 被修改問題(IE8 的 bug)

```js
(function(jQuery, undefined) {
    ...;
    window.jQuery = window.$ = this;
})(jQuery);
```

關於 Date 字串格式 GMT 注意問題

```js
new Date("2019-01-01"); // 正規ISO時間，所以是用ISO時間
new Date("2019/01/01"); // 非正規ISO時間，所以是用本地時間
```

[前端工程研究：關於 JavaScript 中 Date 型別的常見地雷與建議作法 | The Will Will Web](https://blog.miniasp.com/post/2016/09/25/JavaScript-Date-usage-in-details)
JavaScript 物件資料結構

物件回收

當 delete 掉屬性，沒相連跟物件就會被 gc 回收
以瀏覽器的 js 為例，所有的頭都是 window 物件，當 window 屬性與變數 delete 掉時，將會自動回收
(這一點感覺就跟 git 很像，只是 git 的 commit 上面沒有 branch 時候之後會自動被回收)

hoisting

會做 hoisting 有

1. function a(){ xxxx}
2. `var` a = 1;，var 開頭的

但是`var a = function(){xxxx}`，不會做 hoisting

```js
console.log(a); //undefined
var a = 1;
```

程式會變成

```js
var a;
console.log(a); //undefined
a = 1;
```

```js
function a() {
  var test = function() {
    console.log(1);
  };
  function test() {
    console.log(2);
  }
  test();
}

a();
```

同等於

```js
function a() {
  var test;
  function test() {
    console.log(2);
  }
  test = function() {
    console.log(1);
  };
  test();
}

a();
```

** let 區域變數在的時候，不會受到 hoisting 影響 **

let 生命週期是在`{}`裡面，這邊跟 Java 一樣
在宣告時候之前出現變數，是不允許的，就算上{}有 var 變數，也不會顯示出來(這部份看下面範例，很容易忽略)

```js
var a = 1;
console.log(a); // 1
if (true) {
  //這邊因為let關係，所以抓不到外面的var，這一點要注意!!!很容易不小心忽略這個特性
  console.log(a); // can't access lexical declaration
  let a = 2;
  console.log(a); // 2
}
console.log(a); // 1
```

簡稱 ESM(ECMAScript Module)

不管各模組間寫了幾次 import，只會 import 一次，

> 主模組呼叫 `A.js` `B.js` `C.js` 但是`A.js`有呼叫`B.js`
> 所以`B.js`指呼叫一次，不會重新載入

可變的寫法

```js
var a = [];
var b = a;
a.push(1);
console.log(a); //[1]
console.log(b); //[1]
```

不可變的寫法

```js
var a = [];
var b = a;
a = [...a, 1]; // 重新宣告變數，陣列內的 ...a 變數指向原本變數的
console.log(a); //[]
console.log(b); //[1]
```

> 不會改掉記憶體變數，不會影響到別的變數

展開運算子的重要觀念

只有實作迭代器( Iterators ) 的物件，才能使用展開運算子！
預設所有類陣列物件都可以使用(String, Array, Map, Set, arguments )\
預設所有一般物件都不能用，除非有實作迭代器屬性！

[Array.prototype[@@iterator]() - JavaScript | MDN](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/@@iterator)

**容易忽略 for in /for of**

for in 很久以前 js 就有這個東西
但這個是 loop 屬性(非值)

for of 是比較新的語法，
是 loop 出屬性的值

預設參數 (Default Parameters)

```js
function func(a = 1, b, c = 3) {
  console.log(a, b, c);
}
func("a"); //'a',undefined,3
//這邊自己腦補因為ao物件，所以b才沒有掛掉XD?
```

```js
function func(a={})
```

其餘參數 (Rest parameters) ( … )

這玩意可以輕鬆解決 js argument 不能用 array 內建 function
通過這個可以輕鬆得到 array
也不用寫出很複雜的程式

ES6 Setter 跟 Getter

```js
class Rect {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
  get area() {
    return this.width * this.height;
  }
  set area({ height, width }) {
    this.height = height;
    this.width = width;
  }
}
const square = new Rect(3, 3);
console.log(square.area);
```

因為 setter 只能放一個參數，放多個以上會跳出這個訊息
SyntaxError: setter functions must have one argument
後來 Google 有看到用帶`物件`解決
[set 方法只能接受一个参数吗？\_慕课问答](http://www.imooc.com/qadetail/125923)
但後來突然想到可以用`解構賦值`(Destructuring assignment)
完美解決這個問題

物件擴展

[對象的擴展 - ECMAScript 6 入門](http://es6.ruanyifeng.com/#docs/object#%E6%89%A9%E5%B1%95%E8%BF%90%E7%AE%97%E7%AC%A6)

當初用這個想說這個可以改成類似 jQuery `$.extends`

找了一下，還真的有
[[Javascript] 關於 JS 中的淺拷貝和深拷貝 · Larry](http://larry850806.github.io/2016/09/20/shallow-vs-deep-copy/)
原生寫法

```js
//jquery
var $ = require("jquery");

var obj1 = {
  a: 1,
  b: { f: { g: 1 } },
  c: [1, 2, 3]
};

var obj2 = $.extend(true, {}, obj1);
console.log(obj1.b.f === obj2.b.f); // false
//assign
var obj1 = { a: 10, b: 20, c: 30 };
var obj2 = Object.assign({}, obj1);
obj2.b = 100;

console.log(obj1); // { a: 10, b: 20, c: 30 } <-- 沒被改到
console.log(obj2); // { a: 10, b: 100, c: 30 }
```

```js
let aWithDefaults = { x: 1, y: 2, ...a };
// 等同于
let aWithDefaults = Object.assign({}, { x: 1, y: 2 }, a);
// 等同于
let aWithDefaults = Object.assign({ x: 1, y: 2 }, a);
```

閉包該如何 debug?

[善用 JavaScript 的 debugger 指令讓網頁主動進入中斷點 | The Will Will Web](https://blog.miniasp.com/post/2011/03/17/Using-JavaScript-debugger-statement-enter-breakpoints)
有空來寫一篇

var a = function xxx(){

};

a 變數是個 function
但是 console.log(a.name);
會傳出 xxx

這方便在開發者工具看到哪一個 function 比較吃效能
所以閉包也能寫`function xxxx()`

本篇可能之後還會調整，目前沒有加小標題
