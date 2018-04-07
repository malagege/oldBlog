---
layout: post
title: '寫測試程式用js-yaml對設定檔做簡查'
date: 2017-12-13 14:41
comments: true
categories: CI
tags: [js-yaml,yaml,自動化,測試]
---
因為要做動物機要做自動化
載入yaml想檢查一些設定是不是有錯誤
利用上一篇寫的[Exit Codes 程式回傳錯誤碼 « 進擊的程式新手](http://malagege.logdown.com/posts/4442432-exit-codes-program-return-error-code)
在透過Bitbucket做設定
今天進行還滿順利
在此筆記

<!--more-->

```js
yaml = require("js-yaml");
fs = require("fs");

// Get document, or throw exception on error
try {
  var doc = yaml.safeLoad(fs.readFileSync('config.yml', 'utf8'));
  //console.log(doc);
} catch (e) {
  console.log('沒法正常解析yaml');
  console.error(e);
  process.exit(1);
}
```


```js
yaml = require("js-yaml");
fs = require("fs");

// Get document, or throw exception on error
try {
  var doc = yaml.safeLoad(fs.readFileSync('config.yml', 'utf8'));
  //console.log(doc);
} catch (e) {
  console.log('沒法正常解析yaml');
  console.error(e);
  process.exit(1);
}

try {
    let default_path = doc.templates.default.transmission.path;
    let olddownload_path = doc.templates.olddownload.transmission.path;
    let autoPass_path = doc.templates.autoPass.transmission.path;
    let default_month = default_path.substr(32, 2);
    let olddownload_month = olddownload_path.substr(32, 2);
    let autoPass_month = autoPass_path.substr(32, 2);

    let now_month = new Date().getMonth();

    let month_maping = {
        '0' : '01',
        '1' : '01',
        '2' : '01',
        '3' : '04',
        '4' : '04',
        '5' : '04',
        '6' : '07',
        '7' : '07',
        '8' : '07',
        '09' : '10',
        '10' : '10',
        '11' : '10',
    };

    if (month_maping[now_month] != default_month) throw "config.yml裡default_path沒設定正確(" + month_maping[now_month] +")月份名稱";
    if (month_maping[now_month-3] != olddownload_month) throw "config.yml裡olddownload_month沒設定正確(" + month_maping[now_month] + ")月份名稱";
    if (month_maping[now_month-3] != autoPass_month) throw "config.yml裡autoPass_month沒設定正確(" + month_maping[now_month] + ")月份名稱";

} catch (error) {
    console.error(error);
    process.exit(1);
}


console.log('測試正常');
```


```yml,bitbucket-pipelines.yml
image: node:6.9.4

pipelines:
  default:
    - step:
        script: # Modify the commands below to build your repository.
          - npm install -g yaml-lint
          - yamllint config.yml
          - npm install
          - npm test
```


```json,package.json
{
  "name": "yaml_config",
  "version": "1.0.0",
  "description": "",
  "main": "test.js",
  "dependencies": {
    "js-yaml": "^3.10.0"
  },
  "devDependencies": {},
  "scripts": {
    "test": "node test"
  },
  "repository": {
    "type": "git",
    "url": "git+https://malagege@bitbucket.org/malagege/yaml_config.git"
  },
  "author": "",
  "license": "ISC",
  "homepage": "https://bitbucket.org/malagege/yaml_config#readme"
}
```