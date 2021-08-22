---
title: Nodejs 使用 cheerio 做簡單爬蟲
date: 2021-08-22 15:57:00
tags: [modejs, 爬蟲]
categories: Nodejs
---

官方文件:[Chinese README · cheeriojs/cheerio Wiki](https://github.com/cheeriojs/cheerio/wiki/Chinese-README)

<!--more-->

## 安裝 cheerio

```bash=
npm install cherrio -D
```

[npm -i 与npm install -s与-d的区别 - 世界，太精彩 - 博客园](https://www.cnblogs.com/cina33blogs/p/9210931.html)

[Cheerio.js.org vs JsDOM – Hardwork beats talent, when talent doesnt, work hard](https://gurjitmehta.wordpress.com/2017/05/01/cheerio-js-org-vs-jsdom/)



https://qaz33326.pixnet.net/blog/post/46540628-jquery---$.each%E5%A6%82%E4%BD%95%E5%81%9A%E5%88%B0continue,-break

const __dirname = path.resolve();

[Module in Node.js. 越是使用起來覺得理所當然的東西，有時候停下來想想，越是覺得陌生。 | by TD | TD’s note | Medium](https://medium.com/tds-note/module-in-node-js-ada6ca12d380)

```
import { fileURLToPath } from 'url'
import { dirname } from 'path'
const __filename = fileURLToPath(import.meta.url)
const __dirname = dirname(__filename)
```

[Node.js使用ES6 Module | Eddy 思考與學習](https://eddychang.me/node-es6-module)