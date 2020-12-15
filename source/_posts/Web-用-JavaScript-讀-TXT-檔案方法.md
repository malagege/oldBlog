---
title: Web 用 JavaScript 讀 TXT 檔案方法
date: 2020-09-26 23:53:42
tags: [javascript,web,text]
categories: JavaScript
---

最近簡單做了一個 web 文字模板
其實就是簡單正規化去取代
之前也有寫個類似東西
但是都要大幅度去改東西
不是很方便
但這次主要是紀錄 js 讀/寫 txt方法

<!--more-->

## 讀

網路上忘記從哪邊參考過來的
其實我loadFile this抓不到原因是我呼叫方式
事後才想到
```js
function loadFile(file) { 
    var fr=new FileReader(); 
  var that = file;
    fr.onload=function(){ 
        console.log(fr.result);
        // $(that).prev().val(fr.result); 
        //   chgData();
    };
      
    fr.readAsText(file.files[0]); 
  
};
```


## 寫

我採用第一個

- [我的程式學習心得: 【Javascript】匯出txt](http://tsangprogramlearning.blogspot.com/2015/01/javascripttxt.html)
```javascript

function saveTextAsFile( _fileName, _text ) {
    var textFileAsBlob = new Blob([_text], {type:'text/plain'});

    var downloadLink = document.createElement("a");
    downloadLink.download = _fileName;
    downloadLink.innerHTML = "Download File";
    if (window.webkitURL != null) {
        // Chrome allows the link to be clicked
        // without actually adding it to the DOM.
        downloadLink.href = window.webkitURL.createObjectURL(textFileAsBlob);
    } else {
        // Firefox requires the link to be added to the DOM
        // before it can be clicked.
        downloadLink.href = window.URL.createObjectURL(textFileAsBlob);
        downloadLink.onclick = destroyClickedElement;
        downloadLink.style.display = "none";
        document.body.appendChild(downloadLink);
    }

    downloadLink.click();
}

function destroyClickedElement(event) {
    document.body.removeChild(event.target);
}

```

- [html - Javascript:: export to text file - Stack Overflow](https://stackoverflow.com/questions/43135852/javascript-export-to-text-file)

```js
var saveData = (function () {
var a = document.createElement("a");
document.body.appendChild(a);
a.style = "display: none";
return function (data, fileName) {
    var json = JSON.stringify(data),
        blob = new Blob([json], {type: "octet/stream"}),
        url = window.URL.createObjectURL(blob);
    a.href = url;
    a.download = fileName;
    a.click();
    window.URL.revokeObjectURL(url);
};
}());
```

另類寫法
[Download text file javascript - JSFiddle - Code Playground](http://jsfiddle.net/onigetoc/ucsse0sj/)

```html
<a href="data:text/plain;charset=UTF-8,Hello%20World!" download="hello.txt">Download (static)</a>
<a id="programatically" href="" download="date.txt">Download (dynamic)</a>
```

```js
$("a#programatically").click(function(){
    var now = new Date().toString();
    this.href = "data:text/plain;charset=UTF-8,"  + encodeURIComponent(now);
});
```

## 文件模板

其實之前就有想做文件模板
但一直都沒有什麼很好想法
最近突然想用 js-yaml 當作設定
簡單寫的~~爛code~~範例
[JS Bin - Collaborative JavaScript Debugging](https://jsbin.com/jovugelala/1/edit?html,js,output)

jsbin 裡面存檔沒法運作，需要額外寫出來
可以直接用這個連結使用{% asset_link jsbin.jovugelala.1.7z 簡單功能連結 %}

不過未來可規劃

加入 
1. markdown-it
2. 編輯可加入動態參數 editable
3. 進階可加入[Template7](https://idangero.us/template7/)來做動態部分?
4. 動態編輯輸入欄位(input date datetime-local ...等等)
5. 桌面板快速切換工作區?

但有空了解一些 js 方法
之後再實作看看

想想這些現在實力要花很多功力
所以先做個簡單版快速使用