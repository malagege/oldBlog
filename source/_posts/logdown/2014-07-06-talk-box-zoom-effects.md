---
layout: post
title: '說話框放大縮小特效、背景置中'
date: 2014-07-06 19:26
comments: true
categories: 網頁特效
tags: vertical-align
---
前幾天逛到[暗殺教室動畫官網](http://ansatsu-anime.com/main.php)
![暗殺1.png](http://user-image.logdown.io/user/8284/blog/8171/post/208517/qEtVYRBzTvCwqT5HmhGf_%E6%9A%97%E6%AE%BA1.png)
指要把滑鼠移到暗殺老師頭，旁邊就會跑出說話框
<!--more-->

![暗殺2.png](http://user-image.logdown.io/user/8284/blog/8171/post/208517/vh7wWd6TMeWPpxRbg0I4_%E6%9A%97%E6%AE%BA2.png)
然而還可以貼其靠左置中

而且持續拉螢幕一直放大，中間圖片背景還是不變(置中)
正常div置中都是用
```css
width:50%;
margin:0px auto;
```
不過
完完全全不知道是怎麼做到了
想了很久其實很簡單
```css
	background-repeat: no-repeat;
	background-position: center top;
```
當出碰CSS沒有印象有`background-position`這個東西

接下來，說話框他貼齊的方法是用table
```css
.fukidashi table {
	height: 305px;
	width: 225px;
}
.fukidashi td {
	text-align: left;
	vertical-align: middle;
}
```
再好幾年前看到網頁設計要避免使用table
不過這個方法還滿簡單對齊

置於動態效果是用
```javascript
$("#fukidashi").hover(function(){
$(".fukidashi img").stop().animate({"width":"215px","height":"305px"},100);
},
function(){
$(".fukidashi img").stop().animate({"width":"1px","height":"1px"},200);
});
```
沒想到JQuery的animate function可以做出此特效

-------
最後手動實例一下吧
[實例成果](http://plnkr.co/edit/ziBX7eMesKRXoz1urVnY?p=preview)
```html index.html
<!DOCTYPE html>
<html>

  <head>
    <link rel="stylesheet" href="style.css">
    <script data-require="jquery@2.1.1" data-semver="2.1.1" src="//cdnjs.cloudflare.com/ajax/libs/jquery/2.1.1/jquery.min.js"></script>
    <script src="script.js"></script>
    <link href="style.css" rel="stylesheet" />
  </head>

  <body>
    <h1>天線寶寶說你好</h1>
    <div class="wrap">
    <div class="main">
    <div class="image"></div>
    <div class="word">
    <table><tr><td>
      你好
    </td></tr>
    </table>
    </div>
    </div>
    </div>
  </body>

</html>
```

```javascript script.js
// Code goes here
$(document).ready(function(e) {
  $("div.image").hover(function(){
  $(".word").stop().animate({"font-size":"50px"},100);
  },
  function(){
  $(".word").stop().animate({"font-size":"1px"},200);
  });
});
```

```css style.css
div.image {
  position:absolute;
  top:300px;
  left:300px;
  background-image: url('data:image/png;base64,....);
	width:            189px;
	height:           240px;
}
div.wrap {
  height:600px;
  background-repeat: no-repeat;
	background-position: center top;
  background-image: url('data:image/png;base64,...);
}

div.word table{
  width:100%;
  height:100%;
}

div.word{
  position:absolute;
  width:200px;
  height:100px;
  top:200px;
  left:290px;
  font-size:1px;
}
div.word td{
  text-align:center;
  vertical-align:bottom;
}
div.main{
  position:relative;
	height:           600px;
}
```

最後做這個範例也花了我不少時間
由於Easy GO裡的GIMP和Krita都不太會使用... 耗了我滿久的時間(本人美功也很爛)
然後欄的用說話框，就改成字體放大
這個實例可能有點弱。哈。