---
title: 驗證快速檢查JSON方法
date: 2018-10-20 12:44:47
tags: [json]
categories: 實用工具 
---

最近收到一份JSON格式資料
要實作一個API程式
在沒有任何文件囧境
看完好幾千行JSON
後來用Firefox下比對JSON長度
但測試實候發現有些 integer 型態我傳成 string
我發現單純用Firefox肉眼看不行
在想有什麼實用工具可以驗證

<!--more-->

## 檢查JSON type

### Paste JSON as Code -vscode套件
[Paste JSON as Code - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=quicktype.quicktype)
`Paste JSON as Code` 可以產生JSON Schema
還可以產生測試程式
有測試nodejs，還是無法知道哪裡有問題
```
aa = require('./json_check');

aa.testToJson(`{
    "test": "test"
}`);
```

他還能產生`JSON schema`
可以把JSON Schema貼到這個上面
下面會回傳錯誤訊息
[JSON Schema Validator - Newtonsoft](https://www.jsonschemavalidator.net/)

### 線上驗證工具
`JSON Schema Validator`   -   vscode套件本機好像失效，評價3顆星....
[Json Schema with VS Code – Omkar's Tech Blog](https://omkarmore.wordpress.com/2017/04/07/json-schema/)

範例
[GitHub - omkarmore83/json-schema-vscode-example: Examples for Json Schema with Visual Studio code](https://github.com/omkarmore83/json-schema-vscode-example)

## 內建VSCODE就有檢查工具
內建就有的功能，但必須設定schema
[JSON editing in Visual Studio Code](https://code.visualstudio.com/docs/languages/json#_json-schemas-settings)

{% asset_img 1.png "設定地方" %}
這邊可以選擇工作區域

```json
{
    "json.schemas": [
        {
            "fileMatch": [
                ".myconfig"
            ],
            "schema": {
                "$schema": "http://json-schema.org/draft-04/schema#",
                "properties": {
                    "$schema": {
                        "type": "string"         
                    }
                },
                "patternProperties": {
                    "Questions_(EN|DE)$": {
                        "$ref" : "#/definitions/questions-schema"
                    }
                },
                "additionalProperties": false,
                "definitions": {
                    "questions-schema": {
                        "type":"array",
                        "items": {
                            "$ref" : "#/definitions/question-schema"
                        }
                    },
                    "question-schema": {
                        "properties": {
                            "question" : {
                                "type": "string"
                            },
                            "option_1": {
                                "$ref": "#/definitions/option-schema"       
                            },
                            "option_2": {
                                "$ref": "#/definitions/option-schema"       
                            },
                            "option_3": {
                                "$ref": "#/definitions/option-schema"       
                            },
                            "option_4": {
                                "$ref": "#/definitions/option-schema"       
                            },
                            "correct_option": {
                                "type": "integer",
                                "minimum": 1,
                                "maximum": 4
                            },
                            "moreInfo": {
                                "type": "string"
                            },
                            "level": {
                                "type": "integer",
                                "minimum": 1,
                                "maximum": 10,
                                "description": "Level inditates the deficulty of the question. Valid values between 1 and 10"
                            },
                            "category": {
                                "$ref": "#/definitions/category-schema"
                            },
                            "multimedia": {
                                "type": "array",
                                "items": {
                                    "$ref": "#/definitions/multimedia-item-schema"
                                }
                            }
                        },
                        "required":[
                            "question",
                            "option_1",
                            "option_2",
                            "option_3",
                            "option_4",
                            "correct_option"                
                        ],
                        "additionalProperties": false,
                        "defaultSnippets": [
                            {
                                "label" : "New Question",
                                "description": "Creates a New Question template",
                                "body": {
                                    "question": "$1",
                                    "option_1": "$2",
                                    "option_2": "$3",
                                    "option_3": "$4",
                                    "option_4": "$5",
                                    "correct_option": "^$6"                        
                                }
                            }
                        ]
                    },
                    "category-schema": {
                        "enum": [
                            "GEOGRAPHY",
                            "HISTORY",
                            "MATH",
                            "CURRENT_AFFAIRS"
                        ]
                    },
                    "multimedia-item-schema": {
                        "type": "object",
                        "properties": {
                            "id" : {
                                "type": "string"
                            },
                            "type": {
                                "type": "string"
                            },
                            "path": {
                                "type": "string"
                            }
                        },
                        "required": [
                            "id",
                            "type",
                            "path"
                        ],
                        "additionalProperties": false
                    },
                    "option-schema": {
                        "oneOf": [
                            {
                                "type": "string"
                            },
                            {
                                "type": "object",
                                "properties": {
                                    "image": {
                                        "type" : "string"
                                    }
                                }
                            }
                        ]
                    }
                }
            }
        },
    ]
}
```
當然全部都塞到這邊很難管理

```json
{
    "json.schemas": [
        {
            "fileMatch": [
                "/lyric.api.json"
            ],
            "url": "./lyric.schema.json"
        }
    ]
}
```
比較注意的地方`fileMatch`不需要加`.`
加.反而找不到位置
可搭配`*`用在fileMatch

{% asset_img 2.jpg "驗證地方" %}

## 其他工具

[GitHub - java-json-tools/json-schema-validator: A JSON Schema validation implementation in pure Java, which aims for correctness and performance, in that order](https://github.com/java-json-tools/json-schema-validator)

一些JSON SCHEMA範例
[JSON Schema Store](http://schemastore.org/json/)


做完這個，我發現XML要怎麼快速檢單驗證呢
但發現XML有一個叫XSD

一些工具如下，參考來源:[XML Schema(XSD)验证工具？ - 码客](https://oomake.com/question/14248)
Xerces
xmlstarlet
[XML validator and editor for Windows](https://www.xml-buddy.com/ValidatorBuddy.htm)
[Download XML Notepad 2007 from Official Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=7973)
[筆記：Java IDE 從 XSD 轉出 XML – Aya Wong – Medium](https://medium.com/@ayawong/%E7%AD%86%E8%A8%98-java-ide-%E5%BE%9E-xsd-%E8%BD%89%E5%87%BA-xml-3fbd04e0bafc)
[W3C XML Schema (XSD) Validation online](http://www.utilities-online.info/xsdvalidation/)
[Jax 的工作紀錄: [PHP][Java][C#] 用 XSD 驗證 XML](https://jax-work-archive.blogspot.com/2015/02/php-xsd-xml.html)

最後找了範例用在[W3C XML Schema (XSD) Validation online](http://www.utilities-online.info/xsdvalidation/)
可以驗證成功
```xml
<?xml version="1.0"?>
<!DOCTYPE note SYSTEM "http://www.w3school.com.cn/dtd/note.dtd">
<note>
<to>George</to>
<from>John</from>
<heading>Reminder</heading>
<body>Don't forget the meeting!</body>
</note>
```

```xml
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
targetNamespace="http://www.w3school.com.cn"
xmlns="http://www.w3school.com.cn"
elementFormDefault="qualified">

<xs:element name="note">
    <xs:complexType>
      <xs:sequence>
	<xs:element name="to" type="xs:string"/>
	<xs:element name="from" type="xs:string"/>
	<xs:element name="heading" type="xs:string"/>
	<xs:element name="body" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
</xs:element>

</xs:schema>
```

之後有用到，再寫一篇記錄出來