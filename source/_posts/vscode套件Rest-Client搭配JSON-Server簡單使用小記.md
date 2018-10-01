---
title: vscode套件Rest Client搭配JSON-Server簡單使用小記
date: 2018-09-25 21:17:18
tags:
categories: 
---

最近vscode挖到Rest Client套件
這個有點類似postman
可以直接簡單做Server溝通
簡單測試外，感覺還可以順便寫文件XD
但功能好像有點多，所以我就沒玩很深入

<!--more-->

## JSON-Server

首先可以先裝JSON-Server

```
npm install -g json-server
```

設定`db.json`(注意:一定要xxx.json)
```json
{
    "posts":[]
}
```

存檔
```
json-server db.json
```

簡單就架好一個Restful 測試Server XD
想說要模擬分頁、搜尋沒辦法做到
結果一看他的文件
竟然可以做到[typicode/json-server: Get a full fake REST API with zero coding in less than 30 seconds (seriously) #paginate](https://github.com/typicode/json-server#paginate)
最近要練VueJS
剛好可以用這個練習

**NOTE 2018/09/26**
`json-server db.json --static .`可設定`index.html`可以直接連http://localhost/index.html

## REST Client

`###`用來當分格線

```
###
# 取得資料
GET http://localhost:3000/posts


# Content-Type 可以參考
# [四种常见的 POST 提交数据方式 | JerryQu 的小站](https://imququ.com/post/four-ways-to-post-data-in-http.html)
### 新增資料

POST http://localhost:3000/posts  HTTP/1.1
Content-Type: application/x-www-form-urlencoded

test=2&a=1


#  傳JSON，一定要加content-type: application/json 一定要填寫，不然會錯
###
# 新增資料
POST http://localhost:3000/posts  HTTP/1.1
content-type: application/json

{
    "test" : 2
}

###
# PUT 更新資料
PUT http://localhost:3000/posts/1  HTTP/1.1
content-type: application/json

{
    "test2" : "3"
}

###
# DELETE 刪除資料
DELETE http://localhost:3000/posts/1  HTTP/1.1
content-type: application/json

{
    "test2" : "3"
}

```


{% asset_img 1.png "查詢請按紅框" %}

[Huachao/vscode-restclient: REST Client Extension for Visual Studio Code](https://github.com/Huachao/vscode-restclient)
功能真的太多了...


{% asset_link vscode套件Rest-Client搭配JSON-Server簡單使用小記.zip 範例檔案 %}