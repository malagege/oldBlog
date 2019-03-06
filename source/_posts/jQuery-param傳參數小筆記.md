---
title: jQuery $.param傳參數小筆記
date: 2018-08-17 21:17:28
tags: [jQuery, param, form]
categories: jQuery
---

之前我很常在大學自學 jQuery 很常用`$('xxx').serialize()`做表單傳送動作
不過今天上班同事剛好問 form
就看到這個`$.param(xxxx,true)`
好奇這個跟`serialize`哪裡不一樣
這篇當然要好好補足一下
PHP 使用上可能需要用到`$.param`

<!--more-->

網路查了一下
常看到這三個 method 做比較`$.serialize`、`$.serializeArray`和`$.param`

## \$("form").serialize()

基於`$.param`實作
寫\$.ajax 很常用到這個
其實這個跟`$.param`沒有什麼重要關係

## \$("form").serializeArray

產生一個 JavaScript 陣列

```json
[
  {
    "name": "fieldName",
    "value": "value"
  },
  {
    "name": "name",
    "value": "man"
  },
  {
    "name": "sex",
    "value": "male"
  },
  {
    "name": "class[]",
    "value": "math"
  },
  {
    "name": "class[]",
    "value": "chinese"
  }
]
```

一直覺得這個 method 有什麼軟用，不過我來一直重覆看文件
官網有提到

> If the object passed is in an Array, it must be an array of objects in the format returned by .serializeArray()
> [
>
> > { name: "first", value: "Rick" },
> > { name: "last", value: "Astley" },
> > { name: "job", value: "Rock Star" }
> > ]

原來這玩意可以用在這裡(\$.param)

## \$.param

竟然`$().serialize()`可以做到我們想要做的事情
前面有提到`$.param()`就是實作出`$().serialize()`

但我對他第二個參數，比較有興趣
在開方者工作測試

\$.param 除了可以吃`$.serializeArray`傳回的陣列以外
他還能實接對物件做解析

比較需要注意的事情是

```js
var myObject = {
  a: {
    one: 1,
    two: 2,
    three: 3
  },
  b: [1, 2, 3]
};
var recursiveEncoded = $.param(myObject);
var recursiveDecoded = decodeURIComponent($.param(myObject));

alert(recursiveEncoded); // a%5Bone%5D=1&a%5Btwo%5D=2&a%5Bthree%5D=3&b%5B%5D=1&b%5B%5D=2&b%5B%5D=3
alert(recursiveDecoded); // a[one]=1&a[two]=2&a[three]=3&b[]=1&b[]=2&b[]=3
```

php 程式需要做 form 表單送出的時候，需要用`a[one]=1&a[two]=2&a[three]=3&b[]=1&b[]=2&b[]=3`
但這時候 myObject 屬性塞`陣列/物件`，jQuery 預設會轉
\$.param 接收`物件`的時候，第二個參數就有差了
遇設是 false，所以寫 php 都不用調整這個參數
但是 jsp 的就需要了

> 默認的話，traditional 為 false，即 jquery 會深度序列化參數對象，以適應如 PHP 和 Ruby on Rails 框架
> [jQuery ajax 的 traditional 參數的作用 - i33 的個人空間 - 開源中國](https://my.oschina.net/i33/blog/119506)

```
$.param(myObject, false)
```

顯示結果為 `a[one]=1&a[two]=2&a[three]=3&b[]=1&b[]=2&b[]=3`

```
$.param(myObject, true)
```

顯示結果為`a=[object+Object]&b=1&b=2&b=3`

**所以不管怎麼用，checkbox name 還是一定要加[]**

## 寫 PHP checkbox 記得一件事

我覺得我還是一定會忘記 XDD
再寫一次

寫 checkbox 相同 name 的時候，記得在 PHP 變數加個`[]`
ex:

```html
<input name="xxxx[]" type="checkbox" />
<input name="xxxx[]" type="checkbox" />
<input name="xxxx[]" type="checkbox" />
<input name="xxxx[]" type="checkbox" />
```

## jQuery.ajaxSettings.traditional = true;

預設做 ajax 動作實候，呼叫`$.param(xxx,true)`會自動帶後面的 true
但自己寫`$.param`，還是需要手動加(xxx,true)

參考來源：
[jQuery.param() | jQuery API Documentation](https://api.jquery.com/jQuery.param/)
[jquery serialize()、serializearray()已经\$.param 方法 - youxin - 博客园](https://www.cnblogs.com/youxin/p/3891140.html)
