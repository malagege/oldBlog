---
title: CodeIgniter 輸出XML 筆記
date: 2018-10-07 20:41:29
tags: [xml, codeigenter]
categories: PHP
---

最近收到要實作要回傳 XML 的 API
不過，真得已經習慣用 PHP 做 JSON 格式
只需要用 json_encode 就可以搞定

<!--more-->

PHP 本身函式就有對 XML 做解析
不過組 XML 好像沒有比較好方式

有查到[php - XML creation using CodeIgniter - Stack Overflow](https://stackoverflow.com/questions/10361074/xml-creation-using-codeigniter)

[Database Utility Class — CodeIgniter 3.1.9 documentation](https://codeigniter.com/user_guide/database/utilities.html?highlight=xml_from_result#CI_DB_utility::xml_from_result)

但這次 DB 剛好存的資料不是剛好要組出來的資料
最終有網路看到原生支援的方法
加上補`<![CDATA[ ]]!>`
這一段是防止`>`符號產生錯誤
也不用改寫成`&lt;`

[XML CDATA](http://www.w3school.com.cn/xml/xml_cdata.asp)

SimpleXMLElement 加上 CDATA 方法

[php 使用 simpleXML 添加 CDATA 格式数据](https://fukun.org/archives/04192195.html)

[php – 如何使用 SimpleXmlElement 编写 CDATA？ - 代码日志](https://codeday.me/bug/20170905/67209.html)

[PHP: DOMDocument::createCDATASection - Manual](http://php.net/manual/en/domdocument.createcdatasection.php)

```php
<?php
/**
* to show <title lang="en"><![CDATA[Site Title]]></title>   instead of <title lang="en">Site Title</title>
*
*/
class SimpleXMLExtended extends SimpleXMLElement
  {
  public function addCData($cdata_text)
    {
    $node = dom_import_simplexml($this);
    $no   = $node->ownerDocument;
    $node->appendChild($no->createCDATASection($cdata_text));
    }
  }
$xmlFile    = 'config.xml';
// instead of $xml = new SimpleXMLElement('<sites/>');
$xml = new SimpleXMLExtended('<sites/>');
$site = $xml->addChild('site');
// instead of $site->addChild('site', 'Site Title');
$site->title = NULL; // VERY IMPORTANT! We need a node where to append
$site->title->addCData('Site Title');
$site->title->addAttribute('lang', 'en');
$xml->asXML($xmlFile);
?>
```

然後用 PHP 內建多行字串，需要注意用 NOWODC
使用 HEREDOC，裡面有`$`會抓取 PHP 變數
所以需要用 NOWDOC

出輸內容

[Output Class — CodeIgniter 3.1.5 documentation](https://codeigniter.org.tw/userguide3/libraries/output.html#CI_Output::set_content_type)

```php
$this->output
        ->set_content_type('application/json')
        ->set_output(json_encode(array('foo' => 'bar')));

$this->output
        ->set_content_type('jpeg') // You could also use ".jpeg" which will have the full stop removed before looking in config/mimes.php
        ->set_output(file_get_contents('files/something.jpg'));
```

結果

研究完，收到要改寫回傳 JSON 的 api
不過 json 會更簡單
`json(xxxx,true)`
跟

```
$json = [
    "test"=>[
        "number=>[0,1,2,3,4,5]
    ]
]

```

寫法有空整理再發文

2019-01-02 補充 Array to XML 的 Library
[GitHub - Jeckerson/array2xml: [PHP] - Array to XML](https://github.com/Jeckerson/array2xml)

[GitHub - spatie/array-to-xml: A simple class to convert an array to xml](https://github.com/spatie/array-to-xml)
