---
title: 'js做物件reduce運算技巧(map,reduce)'
date: 2019-03-13 22:30:56
tags:
categories:
---

最近想用 js 去做運算
發現使用 reduce 做物件院算發生問題
有爬文找到方法
這邊簡單做紀錄

<!--more-->

```js
this.filterData..reduce( (a , b )=> Number(a.total) + Number(b.total) ).toFixed(4);
```

當初沒有仔細看 reduce 文件
當第一次進去 reduce 代的參數都會帶入 1,2 位置
第一圈可能會沒問題，但是第二圈`a`不是物件，是第一圈 return 的值
所以程式都會有問題
改要怎麼解決這個問題
有找到兩個解法

[functional programming - Javascript reduce on array of objects - Stack Overflow](https://stackoverflow.com/questions/5732043/javascript-reduce-on-array-of-objects)
[上手使用 JavaScript 的 Map、Reduce 吧！](http://fred-zone.blogspot.com/2017/01/javascript-mapreduce.html)

reduce 第二個參數，是 intial value，放入的時候第一個`a`會代 `0`進去
所以後面不會錯
另一個方法是用 map 來整理資料，在做 reduce
第二種會比較舒服

```js
this.filterData
  .map(v => Number(v.total))
  .reduce((a, b) => a + b)
  .toFixed(4);
this.filterData.reduce((a, { total }) => a + +total, 0).toFixed(4);
```

[如何在 Array.forEach 的循环里 break](http://jser.me/2014/04/02/%E5%A6%82%E4%BD%95%E5%9C%A8Array.forEach%E7%9A%84%E5%BE%AA%E7%8E%AF%E9%87%8Cbreak.html)

[js 中**proto**和 prototype 的区别和关系？ - 知乎](https://www.zhihu.com/question/34183746)
