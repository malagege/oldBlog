---
title: electron使用winapi筆記
date: 2018-10-19 23:07:21
tags: [electron]
categories: electron
---

最近做一個蕃茄鐘，但是常常忘記點`開始時間`導致甚麼沒有用到
爬文有查到winapi可以抓到閒置時間
於是看electron是否能串接winapi資訊
就踩到很多個雷

<!--more-->

首先，[winapi - npm](https://www.npmjs.com/package/winapi)
`require('winapi').GetLastInputInfo`這個可以爬到我們要的功能
以為會非常簡單，但是安裝卻不順利

之前有爬到網頁講解node-gyp跟gyp介紹，但現在找不到
但有找到有簡單介紹
>node-gyp
>
>这货是 Node.js 中编译原生模块用的。自从 Node.js v0.8 之后，它就跟 Node.js 黏上了，在此之前它的默认编译帮助包是 node-waf[^3]，对于老 Noder 来说应该不会陌生的。
>GYP

>node-gyp 是基于 GYP[^4] 的。它会识别包或者项目中的 binding.gyp[^5] 文件，然后根据该配置文件生成各系统下能进行编译的项目，如 Windows 下生成 Visual Studio 项目文件（*.sln 等），Unix 下生成 Makefile。在生成这些项目文件之后，node-gyp 还能调用各系统的编译工具（如 GCC）来将项目进行编译，得到最后的动态链接库 *.node 文件。

參考網址:[从暴力到 NAN 再到 NAPI——Node.js 原生模块开发方式变迁 · 一个伪宅级别的码畜。](https://xcoder.in/2017/07/01/nodejs-addon-history/)


以為簡單安裝`node-gyp`就能搞定...，但其實要安裝VSSTUDIO C++跟 python2
注意電腦有裝3的要讓預設變成2
安裝環境建議到VM玩
不然電腦會裝有的沒的東西
我就選擇在VM Win7環境做編譯

簡單記錄安裝`node-gyp`兩個方法

1. `npm install --global --production windows-build-tools`

2. 我發現新版版本自帶安裝選項，不過會裝更多有的沒的東西orz
{% asset_img 1.png "npm run serve:electron" %}


當我以為要成功的時候，但竟然跑出這個東西

{% asset_img 3.png "npm run serve:electron" %}
找到解決方法
以下兩個步驟
1. `npm run build:electron`會產生 winapi_win32_64_electron.node

2. 修改node_module底下winapi的index.js程式
這個是我依照錯誤去找的
沒想到改成electron就能跑XD
{% asset_img 4.png "npm run serve:electron" %}
{% asset_img 2.png "npm run serve:electron" %}


最後，就能順利使用`require('winapi').GetLastInputInfo`函式
他會得到很多秒數

另外
有爬到另一個別人寫好整合
他也包含MAC,Linux,Window 簡稱`idle time`(不知道是不是閒置時間意思??)
[desktop-idle - npm](https://www.npmjs.com/package/desktop-idle)
安裝下去使用不需設定index.js
而且他的秒數比較直覺
後來就用這個函式庫


其他資訊:
[[從libuv/v8到nodejs 系列] ：Libuv - 環境建置與說明 « YJ Blog](http://sj82516-blog.logdown.com/posts/3903966/from-libuv-v8-to-nodejs-series-libuv-environment-setting-and-description)
[Electron通过ffi调用DLL - 陈海峰的博客 - CSDN博客](https://blog.csdn.net/chenhaifeng2016/article/details/75007006)
[[Electron] 如何使用 Node.js 原生模組](https://note.pcwu.net/2017/03/29/js-electron-nodejs-modules/)
[nodejs/node-gyp: Node.js native addon build tool](https://github.com/nodejs/node-gyp#installation)