---
title: Regex 豎旗(flag)的那些事情
date: 2019-01-02 20:32:54
tags: [regex]
categories: Regex
---

很常去搞正規化
但卻沒有認真了解裡面的事情
今天一口氣記錄
其實仔細看也沒很多(= =a)

<!--more-->

flag

- g Global search.
- i Case-insensitive search.
- m Multi-line search.
- u unicode; treat a pattern as a sequence of unicode code points
- y Perform a "sticky" search that matches starting at the current position in the target string. See [RegExp.prototype.sticky | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/RegExp/sticky)

### 心裡疑惑

其實我對`g`這個也滿疑惑的
什麼是全域查詢
平常用 sublime 或 vscode 都沒有這個搜尋選項

`m`多行模式
也讓我很疑惑，是找多行，但感覺跟`g`有關係
可以找多筆

regex 101 產生 JavaScript 語法
`exec`怎麼可以做 while 迴圈

```javascript
const regex = /test/gm;
const str = `test`;
let m;

while ((m = regex.exec(str)) !== null) {
  // This is necessary to avoid infinite loops with zero-width matches
  if (m.index === regex.lastIndex) {
    regex.lastIndex++;
  }

  // The result can be accessed through the `m`-variable.
  m.forEach((match, groupIndex) => {
    console.log(`Found match, group ${groupIndex}: ${match}`);
  });
}
```

### 研究結果

`m`看是否需要一行一行搜尋，通常可能需要搭配`g`才能找出多行資料
一般沒用`m`算單行模式
從這篇來看到[正則表達式之單行模式、多行模式、區分大小寫模式 - ^小七 - 博客園](https://www.cnblogs.com/zengguowang/p/7998754.html) {% asset_link 2.png 備份圖 %}

> 單行模式: (.)點號不匹配換行符，在 windows 中與[^\r\n]等價，並不是刻意匹配任意字符，在單行模式下會改變點號的含義，做到匹配任意字符 s
> 多行模式: 多行模式下^和\$的含義會被改變，普通的含義是匹配整個字符串的開頭和結尾，在多行模式下匹配的就是每一行的行首和行尾，而\A 和\Z 他們的含義始終是匹配整個字符串的開頭和結尾
> 再看這個範例，可以看出差異[Multiple Strings - Regex Tester/Debugger](https://www.regextester.com/102251)
> 簡單來說就是`^xxx$`才會看出差異

`g`其實跟持續搜尋有關係(如:js)，但是 php 會用不同函式(preg_match_all,preg_match)，可以自己玩玩看 regex101
可參考:[[ JS 进阶 ] test, exec, match, replace - kraaas 前端博客 - SegmentFault 思否](https://segmentfault.com/a/1190000003497780)

```
var reg = /\d/;
//第一次匹配
console.log(reg.exec('a123'));
console.log(reg.lastIndex);
//输出
["1"]
  0

第二次匹配
console.log(reg.exec('a123'));
console.log(reg.lastIndex);
//输出
["1"]
  0

```

```
var reg = /\d/g;
//第一次匹配
console.log(reg.exec('a123'));
console.log(reg.lastIndex);
//输出
["1"]
  2

第二次匹配
console.log(reg.exec('a123'));
console.log(reg.lastIndex);
//输出
["2"]
  3

第三次匹配
console.log(reg.exec('a123'));
console.log(reg.lastIndex);
//输出
["3"]
  4

第四匹配
console.log(reg.exec('a123'));
console.log(reg.lastIndex);
//输出
null
  0
```

regex 物件有 exec,test，參數是帶 string
string 型態有 match，參數是帶 regex

要注意 regex exec 跟 test 都吃`g`flag
test 回傳只會傳 true,false
exec 回傳比較特殊，傳回來是一個 array
[当设置全局标志的正则使用 test | MDN](<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/test#%E5%BD%93%E8%AE%BE%E7%BD%AE%E5%85%A8%E5%B1%80%E6%A0%87%E5%BF%97%E7%9A%84%E6%AD%A3%E5%88%99%E4%BD%BF%E7%94%A8test()>)
感覺一不小心結果會得到不一樣的結果

unicode、sticky 好像都是 ES6 東西，先暫時不研究。

[正则的扩展 - ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/regex)
[Edit fiddle - JSFiddle](http://jsfiddle.net/uq55en5o/)

[[JS] RegExp 參數的正確姿勢](https://blog.hinablue.me/js-regexp-can-shu-de-zheng-que-zi-shi/) {% asset_link 1.png 備份圖 %}
[正则表达式多行匹配及声明标识 - hongweigg 的专栏 - CSDN 博客](https://blog.csdn.net/hongweigg/article/details/39579609)
[正则表达式之单行模式、多行模式、区分大小写模式 - ^小七 - 博客园](https://www.cnblogs.com/zengguowang/p/7998754.html)
