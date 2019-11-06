---
title: 對瀏覽器 混合內容（mixed content）的認識
date: 2019-11-06 22:08:03
tags: Web
categories: [https, web]
---

我們常常在改 https 的時候會發現，css 跟 js 沒有調成 https:// 或 //
一些東西會沒有出來，正常 https 載 http (css js) 會失敗
但是 http 載入 https 也會失敗(Firefox)

<!--more-->

## 簡單實驗

```
<html>
<head>
  <title>test</title>
     <link rel="stylesheet" href="http://xxxx/css/reset.css">
</head>
<body>
Hello World!!
</body>
</html>

```

### http網頁 載 http js ,css (自動導 https)

順利執行

### http 網頁 載 https js css 

~~順利執行~~
Chrome 好像正常
但 Firefox 會有問題


### https 網頁 載 http js css

已封鎖載入混和的主動內容「http://xxxx/css/reset.css」

### https 網頁 載 https js css

順利執行

### 實驗簡單總結

簡單說 https 模式下 不能讀 http 東西，因為不被信任
可能會有網頁安全問題

最佳使用方法`//xxxx/css/xxx.css`就不會有這個問題


## 混合內容（mixed content）

主動型混合有分兩種
1. 被動/顯示型混合內容（mixed passive/display content）
2. 主動型混合內容（mixed active content）

### 被動/顯示型混合內容（mixed passive/display content）

```
被動型混合內容清單

以下列出會被視為被動型混合內容的 HTTP 請求：

    <img>（src 屬性）
    <audio>（src 屬性）
    <video>（src 屬性）
    <object> subresource（如果 <object> 發起 HTTP 請求）
```

### 主動型混合內容（mixed active content）

```
主動型混合內容示例

以下列出會被視為主動型混合內容的 HTTP 請求：

    <script>（src 屬性）
    <link>（href 屬性）（包含 CSS 樣式表）
    <iframe>（src 屬性）
    XMLHttpRequest 請求
    fetch() 請求
    所有用到 url 的 CSS 值（@font-face、cursor、background-image……等等）。
    <object>（data 屬性）
```

### 簡單來說

原本想說怎麼分可以載入根不可以載入，爬了一下文章，安全性比較小是可以取得到資源，危險信高的可能不能執行!!

```
現代瀏覽器

現代瀏覽器（Chrome、Firefox、Safari、Microsoft Edge），基本上都遵守了 W3C 的 Mixed Content 規範，將 Mixed Content 分為 Optionally-blockable 和 Blockable 兩類：

Optionally-blockable 類 Mixed Content 包含那些危險較小，即使被中間人篡改也無大礙的資源。現代瀏覽器默認會加載這類資源，同時會在控制台打印警告信息。這類資源包括：

    通過 <img> 標籤加載的圖片（包括 SVG 圖片）；
    通過 <video> / <audio> 和 <source> 標籤加載的視頻或音頻；
    預讀的（Prefetched）資源；

除此之外所有的 Mixed Content 都是 Blockable，瀏覽器必須禁止加載這類資源。所以現代瀏覽器中，對於 HTTPS 頁面中的 JavaScript、CSS 等 HTTP 資源，一律不加載，直接在控制台打印錯誤信息。
```
參考: [关于启用 HTTPS 的一些经验分享（一） | JerryQu 的小站](https://imququ.com/post/sth-about-switch-to-https.html)

## 題外話 用 UserScript 方法

```
unsafeWindow.document.body.innerHTML+='<iframe src="https://xxxxxxxx" width="640px" height="400px" frameborder="0" scrolling="no"></iframe>';
console.log(unsafeWindow.frames[0].window.document.cookie);
```

## 掃 混合內容（mixed content）工具

* [HTTPS Checker Desktop App | HTTPS Checker](https://httpschecker.net/guides/https-checker)
* [GitHub - bramus/mixed-content-scan: Scan your HTTPS-enabled website for Mixed Content](https://github.com/bramus/mixed-content-scan)


## 讓內容能正常執行和預防方法

一開始我已為這跟 CROS 問題類似，但這個完全是不一樣的東西。
但有類似解決方法。使用 CSP

### CSP

這個網頁講的很詳細 [Content Security Policy (CSP) 筆記 - HackMD](https://hackmd.io/@Eotones/BkOX6u5kX)

可以簡單用 .htaccess 測試
```
<IfModule mod_headers.c>
Header set Content-Security-Policy " \
    default-src 'self' xxxxxxx; \
    img-src 'self' data:; \
"
</IfModule>
```

任意網站可以使用 `default-src 'self' *`

[Content-Security-Policy - HTTP Headers 的資安議題 (2) | DEVCORE 戴夫寇爾](https://devco.re/blog/2014/04/08/security-issues-of-http-headers-2-content-security-policy/)

html

```
<html>
<head>
  <title>test</title>
     <link rel="stylesheet" href="http://xxxxxxxxxxxxx/reset.css">
</head>
<body>
Hello World!!
<iframe  src="http://xxxxxxxxx/" width="600" height="100" frameborder="0" scrolling="no"></iframe>
</body>
</html>
~

```


### X-Frame-Options

iframe 所有 https 都能載入，不然不舊所有網站都可以任意被別的網站崁入?

在 header 加 X-Frame-Options 設定
就可以預防倒

Header always append X-Frame-Options SAMEORIGIN

[X-Frame-Options 回應標頭 - HTTP | MDN](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Headers/X-Frame-Options)