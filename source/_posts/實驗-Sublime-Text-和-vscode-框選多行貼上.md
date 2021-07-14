---
title: 實驗 Sublime Text 和 vscode 框選多行貼上
date: 2021-07-14 20:53:35
tags: [sublime,vscode,skill]
categories: 實用工具
---

最近要做 SQL 語句 LIKE 超過 1000 筆，就無法執行。我想透過 Sublime Text 方式去做出來，還踩了一個雷。這個雷是這樣的，我框選 1000 筆(1筆含多行)，貼到我要貼得1000多行，結果貼上超過 1000 多筆資料。

當然簡單研究結果，找出到原因。原因很簡單，就是換行的問題


<!--more-->

Sublime Text 和 vscode 都是一樣結果

## 正規表達式抓到 多行方法

```
((.*\n){1,100})
```
參考: [sublimetext2 - multiple selection every N lines Sublime Text - Stack Overflow](https://stackoverflow.com/questions/25609050/multiple-selection-every-n-lines-sublime-text)

稍微優化

```
(?:(.*\n){1,100})
```

參考:[regex - What is a non-capturing group in regular expressions? - Stack Overflow](https://stackoverflow.com/questions/3512471/what-is-a-non-capturing-group-in-regular-expressions)

### 無法預期

這邊我們抓到資料 100 行資料貼到別的 100 列資料卻無法預期貼上結果。

我們框選內容
{% asset_img OAmJLzq.png 我們框選內容 %}

貼上結果
{% asset_img DhevCDE.png 貼上結果 %}

### 原因

雖然Ctrl + D 框選資料可以包含換行，但是貼上分段式依照換行去變得。直接貼上我實驗的圖片

框選內容:
{% asset_img habK4wk.png 框選內容 %}

結果:
貼上結果前
{% asset_img Mem7p4z.png 貼上結果前 %}
貼上結果後
{% asset_img RQ4yVad.png 貼上結果後 %}

