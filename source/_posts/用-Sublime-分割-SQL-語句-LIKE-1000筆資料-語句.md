---
title: 用 Sublime 分割 SQL 語句 LIKE 1000筆資料 語句
date: 2021-07-14 20:58:32
tags: [sublime,sql]
categories: 實用工具
---

Oralce 有限制 Like 不能超過 1000 多筆。之前有寫過相關文章如下

1. [寫程式使用 split 切割不會注意到的陷阱 | 程式狂想筆記](https://malagege.github.io/blog/2020/10/28/%E5%AF%AB%E7%A8%8B%E5%BC%8F%E4%BD%BF%E7%94%A8-split-%E5%88%87%E5%89%B2%E4%B8%8D%E6%9C%83%E6%B3%A8%E6%84%8F%E5%88%B0%E7%9A%84%E9%99%B7%E9%98%B1/)
2. [Oracle WHERE IN 條件塞入超過 500 筆查詢條件方法 | 程式狂想筆記](https://malagege.github.io/blog/2021/01/12/Oracle-WHERE-IN-%E6%A2%9D%E4%BB%B6%E5%A1%9E%E5%85%A5%E8%B6%85%E9%81%8E-500-%E7%AD%86%E6%9F%A5%E8%A9%A2%E6%A2%9D%E4%BB%B6%E6%96%B9%E6%B3%95/)

但這些虛要手動處理太多，不能一次喝成，有時候資料量真的太大，只能分開查。最近想到 Sublime Text 方法，簡單做個記錄。
還是需要滿多步驟

<!--more-->

## DEMO 範例

```
'這是第 1 行',
'這是第 2 行',
'這是第 3 行',
'這是第 4 行',

```

注意：上面有五行，因為 regex 需要多一行。

### 例外處理

假如框選最後一行，只框選一行。就要多做一個換行
如下：

{% asset_img EZ9AfmU.png title %}

## 產生方法

1. 在 Sublime 使用 regex `((.*\n){1,3})`

{% asset_img XQvraNs.png 1 %}


2. 選擇 Find all 按鈕

{% asset_img eSl1lCW.png 2 %}


3. 按 「Shift + 方向鍵左(←)」 後，再按「Shift + 方向鍵左(↑)」

{% asset_img AW4FHWp.png 3 %}

4. Ctrl + J ，按完就完成兩段句子，最後一行可刪掉

5. 複製兩段 LIKE SQL 語句，中間替代參數也要先設定

{% asset_img faRy4L8.png 5 %}


6. 全部框選`:id:` Ctrl + D，把1,2行複製起來，貼上即可完成

{% asset_img uhqhXg0.png 6-1 %}
{% asset_img n3a9Ou7.png 6-2 %}
{% asset_img yY1rw1h.png 6-3 %}

7. 把最後`,`移除


就可以快樂貼到編輯器查詢。


## 缺點

真的行數到超多筆的話，這個方法應該也沒辦法。

其實這個可以用程式寫，有空寫在補在這。