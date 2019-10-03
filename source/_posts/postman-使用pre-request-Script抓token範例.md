---
title: Postman 使用pre-request Script抓token範例
date: 2018-09-25 21:07:02
tags: [postman]
categories: 實用工具
---

最近同事使用postman抓資料，都需要抓取token所以都需要多一個步驟
所以我就找找有什麼可以不用一直做重複的事情
最後有用出來，但不常用這一定要做一下筆記
<!--more-->
首先，Postman有一個很重要的功能[Variables](https://www.getpostman.com/docs/v6/postman/environments_and_globals/variables)
這個變數可以方便設定
於是我在想有什麼方法可以透過Script幫我做抓token，然後設定到`變數`裡面去

```javascript
var client_id = 'xxxxx';
var client_secret = 'OOOOOO';


var words = CryptoJS.enc.Utf8.parse(client_id + ':' + client_secret);
var base64 = CryptoJS.enc.Base64.stringify(words);
console.log('Basic' + base64);


pm.sendRequest({
    url: 'https://xxxx.com',
    method: 'POST',
    header: 'Authorization: Basic ' + base64,
    body: {
        mode: 'urlencoded',
            urlencoded: [
                    { key: "scope", value: "/main/v1"},
                    { key: "grant_type", value: "client_credentials" },
            ]
    }
}, function (err, res) {
    console.log(res.json());
    var token = res.json().access_token;
    
    pm.environment.set('token','Bearer ' + token);
    
});
```


{% asset_img 1.jpg "放code位置" %}


## refresh token 

[Refresh Token的使用場景以及如何與JWT互動 | 程式前沿](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/2049/)