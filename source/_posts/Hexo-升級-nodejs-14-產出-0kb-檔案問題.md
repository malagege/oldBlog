---
title: Hexo 升級 nodejs 14 產出 0kb 檔案問題
date: 2021-06-03 22:29:57
tags: [hexo]
categories: Hexo
---


最近玩 Vite Nodejs 升級到 14
最近寫 Hexo 時候發現竟然部暑到 Github 都空白
我還以為我 Markdown 有問題
後來 rollback 回去上一版也是一樣
最後發現是 Github 佈署檔案 html 為 0kb
瞬間傻了...

好險，我Git基本知識還行
退回先前版本回去

<!--more-->

爬了很久我發現是 NodeJs 問題
參考: [解决hexo generate 生成的时候index.html为0kb空白的问题 | TonyStudio](https://blog.tcs-y.com/2020/04/26/hexo-index-0kb/)

但我發現我轉到 NodeJS 12 發現還是不能
[hexo 生成的 html 文件为空的问题 · Alan Lee](https://alanlee.fun/2021/02/28/hexo-empty-html/)

> 不成功的话把public文件夹删了重新生成。 这个坑真是大。。。

沒想到這個真的能解決我問題，不知道為什麼 public 刪掉就正常


記得我同學推薦 n 比 nvm 好用
但發現 n 好像不能支援 windows
只好用 nvm(去官方抓release setup版本)

[Windows 10 安裝並使用 nvm 切換 NodeJS 版本 | Welcome.Web.World](https://hsiangfeng.github.io/nodejs/20200107/3738078915/)

## 嘗試升級到 4.2

最近玩 Vite ，我發現還滿有趣的
應該不會移除
一直切換 hexo 不是個好方法
所以我就嘗試看看升級 4.2

```json=
  "dependencies": {
    "hexo": "^4.2.0",
    "hexo-deployer-git": "^0.3.1",
    "hexo-generator-archive": "^0.1.4",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.0",
    "hexo-generator-searchdb": "^1.0.8",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.3.0",
    "hexo-renderer-marked": "^0.3.0",
    "hexo-renderer-stylus": "^0.3.1",
    "hexo-server": "^0.2.0"
  }
```

```
npm install
```

不想升到5版，好像有些設定檔案要調整
哪天迫不得以再說:|

## 其他

[Hexo + github actions 自動化部署 | Winnie's blog](https://op30132.github.io/2020/02/05/github-action/)
