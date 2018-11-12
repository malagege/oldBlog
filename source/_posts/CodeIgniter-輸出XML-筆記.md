---
title: CodeIgniter 輸出XML 筆記
date: 2018-10-07 20:41:29
tags: [xml,codeigenter]
categories: PHP
---

最近收到要實作要回傳XML的API
不過，真得已經習慣用PHP做JSON格式
只需要用json_encode就可以搞定


<!--more-->

PHP本身函式就有對XML做解析
不過組XML好像沒有比較好方式

有查到[php - XML creation using CodeIgniter - Stack Overflow](https://stackoverflow.com/questions/10361074/xml-creation-using-codeigniter)
[Database Utility Class — CodeIgniter 3.1.9 documentation](https://codeigniter.com/user_guide/database/utilities.html?highlight=xml_from_result#CI_DB_utility::xml_from_result)

但這次DB剛好存的資料不是剛好要組出來的資料
最終有網路看到原生支援的方法
加上補`<![CDATA[  ]]!>`
這一段是防止`>`符號產生錯誤
也不用改寫成`&lt;`
[XML CDATA](http://www.w3school.com.cn/xml/xml_cdata.asp)


SimpleXMLElement加上CDATA方法

[php使用simpleXML 添加 CDATA 格式数据](https://fukun.org/archives/04192195.html)
[php – 如何使用SimpleXmlElement编写CDATA？ - 代码日志](https://codeday.me/bug/20170905/67209.html)
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


然後用PHP內建多行字串，需要注意用NOWODC
使用HEREDOC，裡面有`$`會抓取PHP變數
所以需要用NOWDOC



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

研究完，收到要改寫回傳JSON的api
不過json會更簡單
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