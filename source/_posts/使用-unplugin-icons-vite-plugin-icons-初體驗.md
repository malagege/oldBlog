---
title: 使用 unplugin-icons (vite-plugin-icons) 初體驗
date: 2021-10-09 23:16:38
tags: [vite,unplugin-icon,iconify,vite-plugin-icons]
categories: Vue
---

今天正要使用 vite-plugin-icons 看看，發現套件名子改掉了，剛好我順便紀錄。

<!--more-->

## 安裝

```
npm i -D unplugin-icons @iconify/json
```


### SyntaxError: Named export 'SVG' not found. The requested module '@iconify/json-tools' is a CommonJS module, which may not support all module.exports as named exports.

好像某套件不支援 ES6 import 方法，所以造成的?

```
  "type": "module",
```

但我的專案執行某 js 需要支援這個，頭痛QQ。
後來有找到解法[es6 modules - How can I use an ES6 import in Node.js? - Stack Overflow](https://stackoverflow.com/questions/45854169/how-can-i-use-an-es6-import-in-node-js)

```
Node.js >= v13

It's very simple in Node.js 13 and above. You need to either:

    Save the file with .mjs extension, or
    Add { "type": "module" } in the nearest package.json.

You only need to do one of the above to be able to use ECMAScript modules.

Node.js <= v12

If you are using Node.js version 8-12, save the file with ES6 modules with .mjs extension and run it like:

node --experimental-modules my-app.mjs

```

我改成 mjs，npm run xxx-js 就正常了。


## 設定 vite

```javascript=
// vite.config.ts
import Icons from 'unplugin-icons/vite'

export default defineConfig({
  plugins: [
    Icons({ /* options */ }),
  ],
})

```

## 設定 commponent

```htmlembedded=
<script setup>
import IconAccessibility from '~icons/carbon/accessibility'
import IconAccountBox from '~icons/mdi/account-box'
</script>

<template>
  <icon-accessibility/>
  <icon-account-box style="font-size: 2em; color: red"/>
</template>
```

這邊我花了一大段時間查他怎麼取出 Icon。

使用[Icônes](https://icones.js.org/)找出你要的圖

![](https://i.imgur.com/0PxOPde.png)


```javascript=
// icon-park-outline:setting-two
import IconSetting from '~icons/icon-park-outline/setting-two'
```

很特別的是前面 import 的名稱可以自訂。

