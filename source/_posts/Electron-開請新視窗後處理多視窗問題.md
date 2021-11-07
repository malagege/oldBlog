---
title: Electron 開請新視窗後處理多視窗問題
date: 2021-11-07 22:09:21
tags: [electron,nodejs]
categories: Electron 
---

最近實作`new Browserwindow()`多視窗後，關閉原本猜考程式使用`app.close()`，結果發現程式每一個都會關閉，但不知道要怎麼處理，後來有爬到[【Electron Playground 系列】窗口篇 - SegmentFault 思否](https://segmentfault.com/a/1190000038480763) 有講多視窗問題。

<!--more-->


## 所有多視窗觸發程式關閉

```javascript=
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit();
  }
});

```

其實原本程式就有帶進這個 function，所以所有 browserwindow 關掉會做`app.quit()`。

## 處理多視窗方法


裡面有提到[【Electron Playground 系列】窗口篇 - SegmentFault 思否](https://segmentfault.com/a/1190000038480763)，browserwindow有各自`id`，建立 browserwindowMap 紀錄所有browserwindow.id，針對關閉對應所有 id 做特別處理動作。



## 我想到比較完美作法

IpcMain 方法做關閉 Electron 動作，我原本在想上面要怎麼串我的程式，但又覺得上面寫的好麻煩，爬文也找不到更好的方案，最後我想到一個比較好的做法。


```javascript=
window.close()
```

沒錯，就這麼簡單，哈哈。上面提到`window-all-closed`所有 browserwindow 關閉會做`app.quit()`動作。所以不用管 browserwindow是什麼，但網路上沒有人寫這個方法，不知道有沒有安全性問題，但目前我評估應該是不會。

調整程式範例:[解決多視窗關閉問題 · malagege/vue-electron-Pomodoro@f71d5b1 · GitHub](https://github.com/malagege/vue-electron-Pomodoro/commit/f71d5b19f24eadd4209395178062141e390bf4b3)


## 但發現 縮小/放大無法用 window 做到

目前沒有找到方法，網路有很多使用 remote 方法，不過新版好像因為安全性就不能使用。我目前想說子視窗就不做放大縮小按鈕，哈哈。目前只有想到單一window 特別做設定，沒有想到動態視窗一致方法，有想到好方法就補上。