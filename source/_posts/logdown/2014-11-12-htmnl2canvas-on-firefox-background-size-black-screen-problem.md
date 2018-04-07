---
layout: post
title: 'html2canvas在Firefox上background-size黑畫面的問題'
date: 2014-11-12 04:59
comments: true
categories: HTML
tags: [html2canvas,background-size]
---
最近使用html2canvas圖片都無法順利呈現...
最終在https://github.com/niklasvh/html2canvas/issues/288找到解決問題。
https://github.com/niklasvh/html2canvas/commit/e9afe0396019abc39bdb949d78819ae971bf5f2c

不過使用`background-size: xxpx;`數值仍然在Firefox上還是有問題
但是`background-size:cover`和`background-size:contain`可以順力產生圖片
