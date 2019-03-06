---
title: jQuery快速方便載入(load)方法
date: 2019-03-06 21:28:13
tags: [jquery, load]
categories: JavaScript
---

之前[Bootstrap 不知道 Modal 一些事筆記 | 程式狂想筆記](https://malagege.github.io/blog/2019/02/19/Bootstrap-%E4%B8%8D%E7%9F%A5%E9%81%93Modal%E4%B8%80%E4%BA%9B%E4%BA%8B%E7%AD%86%E8%A8%98/)有紀錄 jquery load 事情
但是今天看到還有更神奇的`load`
以前沒甚麼用到這個函式
不過覺得在單一網頁不要讓頁面複雜的時候
可以使用這個方法切出 component

<!--more-->

不過剛開始看 load，想說是單純`.html( data )`載入
看是看了一下文件，功能比我想像的還多
超乎我的想像
所以就心虛寫這篇，虧我寫 jQuery 那麼久都不知道有那麼強大的功能 orz

## 一般使用 load

```js
$("#result").load("ajax/test.html");
```

內容會載入到 選擇器上面。

後面可以放 callback，完成的時候會執行
**備註:此方法會載入 css,js 網頁所有內容**

```js
$("#result").load("ajax/test.html", function() {
  alert("Load was performed.");
});
```

## load 可以指定網頁內容 #div(Loading Page Fragments)

```js
$("#result").load("ajax/test.html #container");
```

這算是我讓我驚訝的功能，而且不會載入 ajax 裡面頁面`html`,`body`,`script`裡面內容
**備註:此方法會`不會`載入 css,js 網頁所有內容**
這是出乎意料的事情

可以在 jQuery 官網[jQuery API Documentation](http://api.jquery.com/)
開起開發者工具來看，
這段 js,css 都會重新載入

```js
$("body").load("/");
```

下面這段 js,css 都不會重新載入

```js
$("body").load("/ #global-nav");
```

不管寫在 body 後面，或者`#global-nav`都不會執行
但對於後面綁定可能真的要寫在 callback 那邊了

## load 可帶參數 post 內容

這個就太簡單了，直接複製官網文件內容

Example: 發送數組形式的 data 參數到服務器。

```js
$("#objectID").load("test.php", { "choices[]": ["Jon", "Susan"] });
```

Example: Same as above, but will POST the additional parameters to the server and a callback that is executed when the server is finished responding.

```js
$("#feeds").load("feeds.php", { limit: 25 }, function() {
  alert("The last 25 entries in the feed have been loaded");
});
```

[.load() : 从服务器载入数据并且将返回的 HTML 代码并插入至 匹配的元素 中。 - jQuery API 中文文档 | jQuery 中文网](https://www.jquery123.com/load/)
