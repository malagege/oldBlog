---
layout: post
title: '[phonegap]安裝套件-battery-status'
date: 2014-12-16 05:03
comments: true
categories: Phonegap
---
首先套件可到http://plugins.cordova.io/搜尋
http://plugins.cordova.io/#/package/org.apache.cordova.battery-status
CMD指令輸入
`phonegap plugin add org.apache.cordova.battery-status `
我把cordova換成phonegap

官網教學是用`plugman`來裝
但這兩種安裝方法好像有差(我還沒搞清楚= =|||)

然後，試試battery-status程式竟然沒有跑出電池資訊
<!--more-->

```
window.addEventListener("batterystatus", onBatteryStatus, false);
function onBatteryStatus(info) {
    // Handle the online event
    alert("Level: " + info.level + " isPlugged: " + info.isPlugged);
}
```

後來查到幾個原因
1.加入cordova.js (我沒加入那個orz)
```html
<script type="text/javascript" src="cordova.js"></script>
```

2.battery-status擺放位置需在deviceready事件處理
```
document.addEventListener("deviceready", onDeviceReady, false);
function onDeviceReady() {
   window.addEventListener("batterystatus", onBatteryStatus, false);
}
function onBatteryStatus(event) {
   console.log("電量: " + event.level + "插入充電器: " + event.isPlugged);
}
```

用手機開Phonegap developer app進去就可以看到成果:D

--
參考資料：
phonegap 增加media plugin範例
http://pcschoolleo.blogspot.tw/2013/11/phonegap-media-plugin.html

http://iclab.csie.chu.edu.tw/course/app-programming/lecture/phonegap-jquery-mobile/
PPT第12章
>PhoneGap框架主要是由手機平台的原生程式碼和JavaScript程式碼所組成，Android就是cordova-x.x.x.jar和cordova-x.x.x.js檔案，所以，框架載入分成原生和JavaScript兩部分。
事件只有window.onload、deviceready、resume和pause事件是我們可以處理的事件，其他都是內部事件。

PhoneGap 與 Cordova 的實際差異
http://blog.tiger-workshop.com/difference-between-phonegap-and-cordova/

PhoneGap API中文幫助文檔——Events（事件）
http://fanli7.net/a/JAVAbiancheng/JAVAzonghe/20120706/181894.html

Plugreg
http://plugreg.com/