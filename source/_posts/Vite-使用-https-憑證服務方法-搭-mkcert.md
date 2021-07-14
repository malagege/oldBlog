---
title: Vite 使用 https 憑證服務方法(搭 mkcert )
date: 2021-07-04 22:36:08
tags: [vite,vue,tls,mkcert]
categories: Vue
---

現在很多開發都需要 HTTPS ，但是在 localhost 預設是沒有 HTTPS，要怎麼在 Vite 快速在開發環境用 HTTPS？爬了一些 Google 文章，來記錄一下。

<!--more-->

## 舊文使用方式

[[Vite] Enabling https on localhost - DEV Community](https://dev.to/web2033/vite-enabling-https-on-localhost-2ckf)

## 安裝 vite-plugin-mkcert

[vite-plugin-mkcert/README-zh_CN.md at main · liuweiGL/vite-plugin-mkcert](https://github.com/liuweiGL/vite-plugin-mkcert/blob/main/README-zh_CN.md)

### 安裝 vite-plugin-mkcert vite & mkcert

**Windows 使用 Administrator 帳號安裝 mkcert!!**
**Windows 使用 Administrator 帳號安裝 mkcert!!**
**Windows 使用 Administrator 帳號安裝 mkcert!!**



```bash
choco install mkcert
# 安裝指令不用做，vite-plugin-mkcert 就會幫忙做
# mkcert -install
# mkcert localhost
```


```bash
# 沒執行過 mkcert 可以用 Administrator 執行
npm install vite-plugin-mkcert -D 
```

Linux 可參考[FiloSottile/mkcert: A simple zero-config tool to make locally trusted development certificates with any names you'd like.](https://github.com/FiloSottile/mkcert)，步驟可能需要自己編譯

### 配置 vite

vite.config.js
```javascript
import { defineConfig } from 'vite'
import mkcert from 'vite-plugin-mkcert'

// https://vitejs.dev/config/
export default defineConfig({
  server: {
    https: true
  },
  plugins: [mkcert()]
})

```

簡單注意:
1. `import mkcert from 'vite-plugin-mkcert'`
2. `https: true`
3. `plugins: [mkcert()]`



### 執行 Vite 

`npm run dev`

第一次會跳出下面圖示，會安裝CA到電腦上面，記得按是。
![](https://i.imgur.com/tW9KWCZ.png)


如果看到不受信任網域，瀏覽器可能需要關掉重開，才能正常看到。


基本上做到這邊就完成了，如果你還有興趣可以往下看。


## 憑證跑去哪?

在`環境變數`加入下面這段
```shell
# window powershell
 $Env:DEBUG = "vite:plugin:mkcert"
# linux 
export DEBUG = "vite:plugin:mkcert"
```

window powershell 詳細使用環境變數方法[關於環境變數 - PowerShell | Microsoft Docs](https://docs.microsoft.com/zh-tw/powershell/module/microsoft.powershell.core/about/about_environment_variables?view=powershell-7.1)

```
The certificate is saved in:
C:\Users\Administrator\.vite-plugin-mkcert\certs\dev.key
C:\Users\Administrator\.vite-plugin-mkcert\certs\dev.pem
```

log 打開可以看到放在家目錄。程式碼也可以看到裡面這樣寫([vite-plugin-mkcert/constant.ts at main · liuweiGL/vite-plugin-mkcert](https://github.com/liuweiGL/vite-plugin-mkcert/blob/main/packages/plugin/src/lib/constant.ts))

