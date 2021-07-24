---
title: JavaScript atob 轉 base64時候處理中文方法
date: 2021-07-24 14:54:43
tags: [javascript,base64]
categories: JavaScript
---

# JavaScript atob 轉 base64時候處理中文方法

我以前一直以為轉 base64 一定都要透過套件來完成，但某一年我看到 JS 也能直接轉 base64(用atob)，我一直以為很完美，直到我今天轉到中文就轉不過了...

<!--more-->

## 開門見山，直接整理網路上解法

- [JavaScript atob / btoa 編解碼不支援 utf8 的解決方案 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10229587)
- [解決 Javascript 中 atob 方法解碼中文字符亂碼問題 - CODING 博客](https://blog.coding.net/blog/resolve-atob-decode-chinese-character-outputting-messy-code-problem-in-javascript)
- [btoa(), atob() 支援中文的方法 | Liullen dev daily](https://nelluil.postach.io/post/btoa-atob-zhi-yuan-zhong-wen-de-fang-fa)
- [原来浏览器原生支持JS Base64编码解码 « 张鑫旭-鑫空间-鑫生活](https://www.zhangxinxu.com/wordpress/2018/08/js-base64-atob-btoa-encode-decode/)

### atob/btoa???

> 由於一些網絡通訊協議的限制,你必須使用 window.btoa() 方法對原數據進行編碼後，才能進行發送。接收方使用相當於 window.atob() 的方法對接受到的 base64 數據進行解碼,得到原數據。例如，發送某些含有 ASCII 碼表中 0 到 31 之間的控制字符的數據。
參考:[解決 Javascript 中 atob 方法解碼中文字符亂碼問題 - CODING 博客](https://blog.coding.net/blog/resolve-atob-decode-chinese-character-outputting-messy-code-problem-in-javascript)

> 记住btoa方法
btoa这个方法名称乍一看，很奇怪，不知道这个单词什么意思。我们可以理解为 B to A，也就是从B到A。那B指什么，A指什么呢？和atob方法一样，B指的是low B普通字符串，A指的是Base64字符。
> 因此，btoa方法表示low B普通字符to Base64字符，也就是Base64编码。
參考:[原来浏览器原生支持JS Base64编码解码 « 张鑫旭-鑫空间-鑫生活](https://www.zhangxinxu.com/wordpress/2018/08/js-base64-atob-btoa-encode-decode/)

我一直看不太懂 function 為什麼取這樣....

### encodeURIComponent

```javascript=
function utf8_to_b64( str ) {
    return window.btoa(encodeURIComponent( escape( str )));
}

function b64_to_utf8( str ) {
    return unescape(decodeURIComponent(window.atob( str )));
}
```

雖然有猜到會用 `encodeURIComponent`，之前 fabar.js 有用到這個處理中文，但是沒有想到 escape / unescape ，這邊不知道有甚麼作用。前端串前端推薦使用，但不是和對外接口或是後湍操作，測試結果會比較長。
[Base64 encode/decode online](https://www.utilities-online.info/base64)網站測試無法解


### 第三方套件

[Javascript base64 - Javascript tutorial with example source code](http://www.webtoolkit.info/javascript-base64.html)

[Base64 encode/decode online](https://www.utilities-online.info/base64)網站測試可以解

上面 ESM 會有問題，所以我改用[dankogai/js-base64: Base64 implementation for JavaScript](https://github.com/dankogai/js-base64)

### MDN 解法

```javascript=
// convert a Unicode string to a string in which
// each 16-bit unit occupies only one byte
function toBinary(string) {
  const codeUnits = new Uint16Array(string.length);
  for (let i = 0; i < codeUnits.length; i++) {
    codeUnits[i] = string.charCodeAt(i);
  }
  return String.fromCharCode(...new Uint8Array(codeUnits.buffer));
}

// a string that contains characters occupying > 1 byte
const myString = "☸☹☺☻☼☾☿";

const converted = toBinary(myString);
const encoded = btoa(converted);
console.log(encoded);                 // OCY5JjomOyY8Jj4mPyY=

```

[Base64 encode/decode online](https://www.utilities-online.info/base64)網站測試可以解

## 總結

自己用的話可以看用哪一個，但對外串接還是用 第三方套件，後端串才不會有問題。