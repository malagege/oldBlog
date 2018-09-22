---
title: Java操作POI修改報表sample檔發生信任活頁簿問題
date: 2018-09-18 23:41:17
tags:
categories:
---

最近使用Java做報表，產生出來資料正確
但開啟會跳出奇怪的東西
後來是要開rar把一個資料刪除sample檔就順利
簡單紀錄

<!--more-->

程式報表處理是這樣的

1. 讀取Excel SAMPLE檔案
2. 寫入Excel資料
3. 另存新檔到xxxx.xlsx

今天就是改Excel  SAMPLE檔案存檔
程式順利執行執行
但是跳出錯誤訊息


{% asset_img 1.jpg "Excel 錯誤訊息1" %}
{% asset_img 2.jpg "Excel 錯誤訊息2" %}

雖然會自動修復...，但每次產生新報表這樣也很麻煩

{% asset_img 3.jpg "用rar開啟sample.xlsx檔案刪除calcChain.xml" %}
用rar開啟sample.xlsx檔案刪除calcChain.xml
後來副理跟我說要故這個才能解決。

不知道是什麼原因才造成的
先筆記