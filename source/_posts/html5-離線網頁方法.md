---
title: html5 離線網頁方法
date: 2021-06-02 20:56:21
tags: [html5]
categories: HTML5
---

最近寫個小APP
想說沒網路也能用
沒想到也是一個大學問

<!--more-->

## 舊的使用方法(以廢棄)

建議不用使用[Using the application cache - HTML: HyperText Markup Language | MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Using_the_application_cache)
以廢棄

使用參考

[[HTML5試試看-25] 離線程式 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10058385)


[有趣的HTML5：离线存储 - SegmentFault 思否](https://segmentfault.com/a/1190000000732617)


## sw

[service worker 实现离线缓存](https://mp.weixin.qq.com/s/aboA9dtCK6t0fzs0JU58Iw)

[PWA-3小时带你实现渐进式WebAPP_哔哩哔哩 (゜-゜)つロ 干杯~-bilibili](https://www.bilibili.com/video/BV1wt411E7QD?p=10)


[使用Service worker實現加速/離線訪問靜態blog網站_谷歌開發者 - 微文庫](https://www.gushiciku.cn/dc_tw/108051214)

### 實作

https://developers.google.com/web/fundamentals/codelabs/offline?hl=zh-tw
參照裡面做法
1. index.html
```htmlembedded=
<script>
if('serviceWorker' in navigator) {
  navigator.serviceWorker
           .register('/sw.js')
           .then(function() { console.log("Service Worker Registered"); });
}
</script>

```

2. sw.js

```javascript=
self.addEventListener('install', function(e) {
    console.log('[Service Worker] Install');
});

var cacheName = 'js13kPWA-v2';
var appShellFiles = [
    '/',
    'index.html',
    'https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.0.1/html5-qrcode.min.js'
];

var contentToCache = appShellFiles;

self.addEventListener('install', function(e) {
    console.log('[Service Worker] Install');
    e.waitUntil(
      caches.open(cacheName).then(function(cache) {
            console.log('[Service Worker] Caching all: app shell and content');
        return cache.addAll(contentToCache);
      })
    );
  });

  self.addEventListener('activate', event => {
    event.waitUntil(self.clients.claim());
  });
  
  self.addEventListener('fetch', event => {
    event.respondWith(
      caches.open(cacheName)
        .then(cache => cache.match(event.request, {ignoreSearch: true}))
        .then(response => {
        return response || fetch(event.request);
      })
    );
  });
```

就順利成功

有空再深入研究