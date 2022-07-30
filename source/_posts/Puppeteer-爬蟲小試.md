---
title: Puppeteer 爬蟲小試
date: 2022-07-31 02:22:26
tags: [爬蟲, puppeteer,js]
categories: 爬蟲
---

## 參考文章

[淺嚐 WebDriverIO - Eden Liu](https://eden-liu.com/frontend/taste-webdriverio/)
[【Day8】selenium-webdriver：爬蟲起手式，帶你認識所見即所得的爬蟲工具 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10241791)
[Day 22 Puppeteer處理lazy load, SSR, 反爬蟲防禦, 高效地爬大量數據 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10204195)

<!--more-->

## 安裝環境

1. 建好 node 初始專案

```bash=
npm init

```

2. 安裝

```
npm i puppeteer

```


3. 執行程式看看

```javascript=
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('https://example.com');
  await page.screenshot({ path: 'example.png' });

  await browser.close();
})();

```


## page.click 無效解決方法

有時候執行page.click無效，目前不確定甚麼原因，有找到方法解決，參考 [Navigation after click not working regardless of actions I take · Issue #3670 · puppeteer/puppeteer](https://github.com/puppeteer/puppeteer/issues/3670)

```javascript=
 async function nativeClick(page, button) {
   await page.evaluate((button) => {
     document.querySelector(button).click();
   }, button);
 }
 ```
 


## tag 裡面文字

HTML Element 裡面可以再做 `querySelector` 在做搜尋元件動作，也可以用XPATH，但XPATH不太熟的話，建議用JS去做會比較簡單。

[Javascript .querySelector find \<div\> by innerTEXT - Stack Overflow](https://stackoverflow.com/questions/37098405/javascript-queryselector-find-div-by-innertext)
    
    

## iframe 處理方法

```javascript=
const frameHandle = await page.$("iframe[id='frame1']");
const frame = await frameHandle.contentFrame();
frame.click(xxx)
```

frame用法跟 page差不多。

[iFrames in Puppeteer](https://chercher.tech/puppeteer/iframes-puppeteer)

[[Node.js打造API] 設定joi與config dotenv環境參數 — 1010Code](https://andy6804tw.github.io/2018/01/01/joi-config-dotenv/)

## base auth



[Implement HTTP Authentication API · Issue #426 · puppeteer/puppeteer](https://github.com/puppeteer/puppeteer/issues/426)

## dialog 處理

[Puppeteer，該如何判斷登入失敗跳出提示的動作？（順便想記錄第一次用到 Promise.race() XD） | 小馬彬的部落格](https://littlehorseboy.github.io/2020/07/11/202007-puppeteer-dialog-promise-race/#%E7%88%AC%E8%9F%B2%E7%9B%AE%E6%A8%99%E7%B6%B2%E7%AB%99%E8%AA%AA%E6%98%8E)

## deno 編譯程式

可參考:[deno 牌 puppeteer，真香! | 七日打卡_胡琦 - MdEditor](https://www.gushiciku.cn/pl/grDK/zh-tw)

我有在 deno 編譯成exe程式，不過在電腦使用不能正常執行，但後來發現需要設定環境變數`PUPPETEER_EXECUTABLE_PATH`指定你 Chrome 安裝路徑也可以跑。

## 其他

[nodejs打包成桌面程式（exe）的進階之路 - ⎝⎛CodingNote.cc ⎞⎠](https://codingnote.cc/zh-tw/p/186803/)

[爬蟲、E2E 測試兩相宜的好工具 - Puppeteer | 卡斯伯 Blog - 前端，沒有極限](https://wcc723.github.io/development/2020/03/01/puppeteer/)
[how to minimize browser to tray · Issue #4513 · puppeteer/puppeteer](https://github.com/puppeteer/puppeteer/issues/4513)
[Puppeteer爬蟲常用技巧 | 只是個打字的](https://blog.typeart.cc/using-puppeteer-crawler-common-skills/)
[奇妙的一行JS程式碼之完整解析 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10228789)