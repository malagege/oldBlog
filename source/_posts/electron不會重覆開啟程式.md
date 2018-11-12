---
title: electron不會重覆開啟程式
date: 2018-10-20 00:15:22
tags: [electron]
categories: JavaScript
---

最近爬到能electron不會重新開啟程式方法
筆記筆記

<!--more-->

```js
// 點擊圖標(桌面快捷方式)檢查當前活動實例的個數
const isSecondInstance = app.makeSingleInstance(() => {
  if (mainWindow) {
    if (mainWindow.isMinimized()) {
      mainWindow.restore(); // 窗口從最小化恢復時觸發
    }
    mainWindow.show();
    // mainWindow.maximize();
    mainWindow.focus();
  }
});
if (isSecondInstance) {
  app.quit();
}
```

參考[使用 electron-vue 搭建桌面應用開發模板 - 神馬文庫](https://www.smwenku.com/a/5b82232d2b717737e032a0d9/)
{% asset_link 1.png 備份圖 %}