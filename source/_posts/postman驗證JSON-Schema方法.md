---
title: postman驗證JSON Schema方法
date: 2018-11-05 20:17:57
tags: [postman, json schema]
categories: 實用工具
---

最近在實作 API，發現上次只有做 VSCODE 檢查
postman 假如可以用的話，也很方便

<!--more-->

```js
var schema = {
    "$schema": "http://json-schema.org/draft-06/schema#",
        ......(略)
    }
};

pm.test("Check JSON", function () {
    var jsonData = pm.response.json();
  var valid = tv4.validate(jsonData, schema, false, true);
  if (valid !== true) {
      console.log(tv4.error);
      tests['tv4.error:' + tv4.error] = true;
  }
  pm.expect(valid).to.be.true;
});
```

只要在`pre-test`放這段就可以檢查了

參考來源:

- [How do I test JSON schema in postman using tv4? - Stack Overflow](https://stackoverflow.com/questions/48098530/how-do-i-test-json-schema-in-postman-using-tv4)
- [javascript - Debug output in tests - Stack Overflow](https://stackoverflow.com/questions/31157675/debug-output-in-tests)

2018-11-07

最近應為錯誤發現不會提示顯示哪裡錯誤
所以爬到[How do I test JSON schema in postman using tv4? - Stack Overflow](https://stackoverflow.com/questions/48098530/how-do-i-test-json-schema-in-postman-using-tv4)
修改一下程式碼

```javascript
pm.test("Check JSON", function() {
  var jsonData = pm.response.json();
  var valid = tv4.validateMultiple(jsonData, schema, false, true);
  if (valid.errors) {
    valid.errors.forEach(
      (value, index) => (tests[value.schemaPath + ":" + value.message] = true)
    );
  }
  pm.expect(valid.errors.length === 0).to.be.true;
});
```

最後，假如要用上次 VSCODE
`setting.json`好像沒辦法自動設定所有 json 檔案
~~只能單一設定~~

```json
{
  "json.schemas": [
    {
      "fileMatch": ["xxx.api.json"],
      "url": "./xxx.schema.json"
    },
    {
      "fileMatch": ["oooo.json"],
      "url": "./oooo.schema.json"
    }
  ]
}
```

~~未來有找到好方法再補~~
有找到[VSCode 配置文件的变量索引 - 知乎](https://zhuanlan.zhihu.com/p/44967536)

```json
{
  //不能使用
  "json.schemas": [
    {
      "fileMatch": ["${fileBasenameNoExtension}.json"],
      "url": "./${fileBasenameNoExtension}.schema.json"
    }
  ]
}
```

~~明天測試看能不能用~~

2018-11-09
確定不能使用

REST Client 好像沒有 JSON Schema 檢查工具

一些 github 上檢驗工具
[ebdrup/json-schema-benchmark: Benchmarks for Node.js JSON-schema validators](https://github.com/ebdrup/json-schema-benchmark)

2019-05-22
另類檢查寫法

```js
var data = JSON.parse(responseBody);

pm.test("response must be valid and have a body", function() {
  // assert that the status code is 200
  pm.response.to.be.ok;
  // assert that the response has a valid JSON body
  pm.response.to.be.withBody;
  pm.response.to.be.json;
});

pm.test("respnse body has xxxx ?", function() {
  pm.expect(data.response.xxxx).is.not.empty;
  pm.response.to.not.have.jsonBody("error");
});
```

參考來源:
[Test scripts | Postman Learning Center](https://learning.getpostman.com/docs/postman/scripts/test_scripts/)
