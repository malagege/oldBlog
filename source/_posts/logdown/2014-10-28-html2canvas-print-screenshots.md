---
layout: post
title: '使用html2canvas截圖列印'
date: 2014-10-28 07:44
comments: true
categories: HTML
tags: [html2canvas,js]
---
路過[以html2canvas制作頁面截圖列印](http://wengsi62.blogspot.tw/2013/03/html2canvas.html)教學

覺得還不錯，筆記筆記！
<!--more-->

>
 以html2canvas制作頁面截圖列印
網上有很多截圖的Service, 如webkit2png或khtml2png，但始終也要經過第三方處理，對於一些內部應用好像不太方便。
>
HTML2CANVAS是完全以Client Side Javascript 來將網頁截圖，由於需要用到HTML5的CANVAS ELEMENT，所以對瀏覽器有一定要求，例如 IE9以下並不支援 (但不知道如果用EXCANVAS可否彌補瀏覽器支援上的不足，稍後再試吧)。
>
HTML2CANVAS
官方：http://html2canvas.hertzen.com/
下載：https://github.com/niklasvh/html2canvas
>
開始編寫時在官方下載了0.34版本，使用時發現 IE9可正常運行，但Chrome只能於ready時執行，而無法由button click event執行。後來改用了0.4版本，卻輪到 IE不行。接著發現其中的renderElement function 中有null value的error, 修改後IE終於亦可正常運作了。
>
紅色為html2canvas.js新增部份
```javascript
case "INPUT":
        if (/^(text|url|email|submit|button|reset)$/.test(element.type) && (element.value || element.placeholder || "").length > 0){
          renderFormValue(element, bounds, stack);
        }
```
>
亦可下載修改後的html2canvas.js :
https://docs.google.com/file/d/0B9ookpf4ArZcMHhqemVkTzBCVlU/edit?usp=sharing
>
例子: index.html(http://plnkr.co/edit/VR30GO)
>
```html
<!doctype html>
<html>
<head>
  <script src="jquery.js"></script> <!-- version: 1.9.1 -->
  <script src="html2canvas.js"></script> <!-- version: 0.4.0 modify -->
</head>
<body>
  <h1>Example</h1>
  <pre>HTML2CANVAS to new window and print</pre>

  <button class="print">Print</button>

  <!-- Button event must after button element, it is not run insert ready() -->
  <script>
   $(".print").on("click", function(event) {
 html2canvas($('body'), {
  onrendered: function(canvas) {

    var newWind=window.open('', "Print");
    <!-- Must use html5 -->
    newWind.document.write("<!doctype html><html><body><img src='"+canvas.toDataURL()+"'/></body></html>");

    newWind.print();
   }
 });

  });
  </script>
</body>
</html>
```

File Download (include index.html, html2canvas.js, jquery.js):
https://docs.google.com/file/d/0B9ookpf4ArZcSlB2OXN4c1pGQ00/edit?usp=sharing
