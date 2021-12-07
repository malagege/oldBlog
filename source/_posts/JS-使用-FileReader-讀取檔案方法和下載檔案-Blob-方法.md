---
title: JS 使用 FileReader 讀取檔案方法和下載檔案(Blob)方法
date: 2021-11-07 22:12:30
tags: [javascript,filereader,blob]
categories: JavaScript 
---

想畫個圖，有時候過很久再回來看這個就忘記怎麼用，但想簡單使用所以用 `sequenceDiagram` 來描述內容，哈哈，懶得自己畫圖描述。

<!--more-->

{% mermaid sequenceDiagram %} 
    JavaScript->>JavaScript: 建立new FileReader();
    JavaScript->>JavaScript: 建立 input_file change 事件。
    JavaScript->>JavaScript: 建立 fileReader load 事件。
    HTML-->>JavaScript: 人為出發HTML input(file)檔案，會觸發file change Event
    JavaScript-->>JavaScript: 觸發 input change 事件!
    participant JavaScript
    Note right of JavaScript: 使用 fileReader.readAs*(this.files[0])事件讀第一個files[0]，會出發filereader load Event
    Note right of JavaScript: 這邊的 readAs* 圖片要用 readAsDataURL，文字要用 readAsText。
    JavaScript-->>JavaScript: 觸發 load 事件!
    Note right of JavaScript: 使用 this.result 讀出檔案內容
{% endmermaid %}

```htmlembedded=
<input id="fileUploader" type="file" />
<div>圖片預覽：</div>
<img id="imageView" src="" style="width:200px; height200px;" />
```

```javascript=
var fileUploader = document.getElementById("fileUploader");
var imageView = document.getElementById("imageView");
//用來讀取file資料的FileReader
var fileReader = new FileReader();

//監控#fileUploader的值變化
fileUploader.addEventListener("change", function(event) {
  if (this.files.length > 0) {
  	//有選取file時，使用fileReader讀取file資料
    //readAsDataURL可以將讀取到的file資料轉成
		//data:......的URL型式
    fileReader.readAsDataURL(this.files[0]);
  }else{
  	//沒有選取file時，例如選擇取消，
    //將<img>的src設成""
  	imageView.src = "";
  }
}, false);

fileReader.addEventListener("load", function(event) {
	//讀取後設定<img>的src
  imageView.src = this.result;
}, false);
```

參考:[生活記事簿: 使用FileReader讀取file資料 - javascript](http://hklifenote.blogspot.com/2016/08/filereaderfile-javascript.html)

## FileReader.readas*

> 下述方法可以將 File / Blob 物件轉換成 TypedArray
>
>    FileReader.readAsArrayBuffer()：以 TypedArray 的方式顯示檔案內容。
>    FileReader.readAsDataURL()：以 data:`<MIME Type>`;base64,... 的方式顯示檔案內容，讀取的資料是圖檔的話會轉成 Base64，**可以直接用於 `<img src="BASE_64">` 中；讀取的資料是 CSS 等若無法直接辨識的檔案類型，會轉成 application/octet-stream 的 Base 64 檔案。
>    FileReader.readAsText()：以純文字的方式顯示檔案內容，預設是 UTF-8。
>    FileReader.readAsBinaryString()：以原始二進制顯示檔案內容。
>
>    ⚠️ 這個 DataURL 取得的字串不能直接進行 Base64 解碼，必須把前綴 data:*/*;base64, 從字串裡刪除以後，再進行解碼。

上面範例是使用`FileReader.readAsDataURL()`放到圖片去，要讀取文字要用`FileReader.readAsText()`

詳細可以看: [[WebAPIs] Blob, File 和 FileReader | PJCHENder 未整理筆記](https://pjchender.dev/webapis/webapis-blob-file/)，裡面有一堆寶。

最近用 Vue 元件做讀檔動作有參考: [Creating a Vue.js File Reader Component Using the FileReader API | DigitalOcean](https://www.digitalocean.com/community/tutorials/vuejs-file-reader-component)，順便改個可以客製內容，可以參考[匯入匯出基本功能 · malagege/vue-myanimelist@68ba651 · GitHub](https://github.com/malagege/vue-myanimelist/commit/68ba65151c370e15a0c70ae95055fa8d2411749a)。

## Blob

```javascript=
// new Blob(array [, options])
// - array 是字元串或二進位物件（ArrayBuffer, ArrayBufferView, Blob, DOMString）
// - options 用來指定 MIME Type

var htmlFragment = '<div><h1>Hello Blob</h1></div>';
var htmlBlob = new Blob([htmlFragment], { type: 'text/html' });
// Blob {size: 30, type: 'text/html'}
```

比較特別建構時候用 array 去組，有 type,size 屬性可以用。

## files[0] ==> blob 

簡單說，`event.target.files[0]`可以放在程式裡面當變數，帶入fileReader 使用。

MDN文件 [File - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/File)



## 儲存 Blob（createObjectURL）

```javascript=
 function saveData(blob, fileName) { 
            const a = document.createElement('a');
            document.body.appendChild(a);
            a.style = 'display: none';
            // 將 blob 放到 URL 上
            let url = window.URL.createObjectURL(blob);
            a.href = url;
            a.download = fileName;
            a.click();
            // 釋放記憶體
            a.href = '';
            window.URL.revokeObjectURL(url);
            }
```


可以用來下載存文字，或實務上做 JSON 匯出動作，可以參考[匯入匯出基本功能 · malagege/vue-myanimelist@68ba651 · GitHub](https://github.com/malagege/vue-myanimelist/commit/68ba65151c370e15a0c70ae95055fa8d2411749a)。

這邊下載功能我參考: [[WebAPIs] Blob, File 和 FileReader | PJCHENder 未整理筆記](https://pjchender.dev/webapis/webapis-blob-file/)，改個程式修正就能正常使用。



### createObjectURL

可以給與 Blob 物件，可以用`blob://連結`，實作認知會比較快。可以在 `console.log`輸入下程式碼。

```javascript=
var htmlFragment = '<div><h1>Hello Blob</h1></div>';
var htmlBlob = new Blob([htmlFragment], { type: 'text/html' });
 window.URL.createObjectURL(htmlBlob);
// 'blob:chrome://new-tab-page/a0d1ce2f-3d38-46f5-ba72-27c91a76a226'
```

![](https://i.imgur.com/265F5Bb.png)


更多內容可以看[HTML5 神奇的 Object URL：不用後端，前端便能產生獲取指定物件的網址 | by 柯政祥 (Zheng-Xiang Ke) | Medium](https://medium.com/@kf99916/html5-%E7%A5%9E%E5%A5%87%E7%9A%84-object-url-%E4%B8%8D%E7%94%A8%E5%BE%8C%E7%AB%AF-%E5%89%8D%E7%AB%AF%E4%BE%BF%E8%83%BD%E7%94%A2%E7%94%9F%E7%8D%B2%E5%8F%96%E6%8C%87%E5%AE%9A%E7%89%A9%E4%BB%B6%E7%9A%84%E7%B6%B2%E5%9D%80-6df283d58505)。



## Blob 其他相關

### Blob 怎麼用 Form(網頁表單) 傳資料

```javascript=
var formData = new FormData();
formData.append("name", blob, filename);
```

Blob 變數可以放入 `event.target.files[0]`

參考:[javascript - What is the form input type for posting blob from an HTML Form? - Stack Overflow](https://stackoverflow.com/questions/37218585/what-is-the-form-input-type-for-posting-blob-from-an-html-form)

詳細可以看 MDN 文件 [FormData.append() - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/FormData/append)，有兩種放入 FormData 方法。

### Blob 播放影片相關

原本在想這個有甚麼好處，但看到網路上有提到這個是邊下載邊播放影片，但我映像中 video mp4 好像能這樣做，但不確定是不是其他格式不行?

[关于blob - 知乎](https://zhuanlan.zhihu.com/p/166014722)
[MediaSource的使用以及封装MP4转加载Blob的插件 - Postbird - 猫既吾命](http://www.ptbird.cn/javascript-mediasource-mpt-to-blob.html)