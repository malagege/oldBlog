---
title: axios 操作小記
date: 2022-07-31 02:19:47
tags: [javascript,axios]
categories: JavaScript
---

太久沒寫前端就忘了，簡單紀錄。

<!--more-->

## axios 是物件

[十八日目：JavaScript　axiosノ章 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10247560)

## Creating an instance

建立 axios 客製設定實例。

```javascript=
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```

## axios.data

### data 

1. 可放'Country=Brasil&City=Belo Horizonte'字串

2.   // - Browser only: FormData, File, Blob
  // - Node only: Stream, Buffer
  
  
 

## Form 表單傳資訊

[[Node.js] 使用 axios post form-data](https://oranwind.org/javascript-axios-post-form-data/)

```javascript=
  const formData = new FormData()
  Object.keys(data).forEach(key => {
    formData.append(key, data[key])
  })

```

[axios 基本使用 & Config - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10212120)
