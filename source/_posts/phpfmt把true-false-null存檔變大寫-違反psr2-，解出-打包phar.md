---
title: 'phpfmt把true,false,null存檔變大寫(違反psr2)，解出/打包phar'
date: 2018-07-18 23:41:48
tags: [phpfmt,psr2,phar,php,vscode]
categories: 實用工具
---

由於目前公司開發團隊，有自訂PHP程式風格
但是規則跟PSR規則不太一樣
其中一個就是要true,false要大寫(drupal規範好像是要大寫)
平常我也都小寫orz
不知道當初訂製這個規則是不是有開發過drupal
最近使用phpfmt一直在找解決這個問題(常常忘記大寫orz)
最後研究出來，當然一定要筆記一下
有需要可能可以參考這篇
應該沒有什麼人需要吧 呵呵

<!--more-->

找出phpfmt路徑
`%USERPROFILE%\.vscode\extensions`

進入phpfmt路徑(可搜尋phpfmt)
裡面有`fmt.phar`檔案

phar是類似php多隻程式打包成一個檔案，有一點跟jar一樣
但怎麼不能用zip打開orz
不過說真的打包過程很麻煩樣子
composer也不能直接做phar(可能沒有找到)
最後有Google到方法


### 解出phar
[php归档格式：phar文件详解（创建、使用、解包还原提取） - CSDN博客](https://blog.csdn.net/u011474028/article/details/54973571)
```php
$phar = new Phar('fmt.phar');
$phar->extractTo('fmt'); //提取一份原項目文件
$phar->convertToData(Phar::ZIP); //另外再提取一份，和上行二選一即可
```

### 打包phar
參考[苗栗縣教育處資訊教育中心 - 打包多個php檔案變成phar執行檔](https://it.mlc.edu.tw/index.php/2012-03-24-08-48-16/2012-03-24-08-49-32/27-php/54-php-phar)

```php
<?php
$p=new Phar('./fmt.phar');
$p->setStub(file_get_contents('fmt.stub.php'));//入口網頁
$p->buildFromDirectory('.');//不確定要有
```


### 無法解出phar問題

因為在公司不知道為什麼?phar一直失敗
* 注意資料夾是不是唯讀
* 確定php.ini有沒有把phar.readonly = Off
* phar.readonly前面有沒有把; del掉(我就是卡在這邊orz)

要怎麼知道command line 的php.ini是吃哪裡?預設應該是php目錄裡面（PS:phpfmt是對應OS環境變數path裡面php)
[easyphp - How to find the php.ini file used by the command line? - Stack Overflow](https://stackoverflow.com/questions/2750580/how-to-find-the-php-ini-file-used-by-the-command-line)
`php -i`可以看裡面設定內容，可以看到phar.readonly有沒有關閉才能正確執行

phar參考連結
* [akalongman/fmt: Tooling for PHP - testing, code coverage and formatting](https://github.com/akalongman/fmt)
* [如何解包还原一个phar文件](https://newsn.net/say/php-phar-extract.html)
* [PHP的学习--使用phar打包 - 疯狂的原始人 - 博客园](http://www.cnblogs.com/CraryPrimitiveMan/p/6158196.html)
* [苗栗縣教育處資訊教育中心 - 打包多個php檔案變成phar執行檔](https://it.mlc.edu.tw/index.php/2012-03-24-08-48-16/2012-03-24-08-49-32/27-php/54-php-phar)
* [easyphp - How to find the php.ini file used by the command line? - Stack Overflow](https://stackoverflow.com/questions/2750580/how-to-find-the-php-ini-file-used-by-the-command-line)
* [Phar建立與使用 | ScarShow | 刀疤說](https://scar.tw/article/2013/01/09/php-phar-create-and-use/)

修改完成
{% asset_img fmt.gif vscode結果  %}


最後修改完php
做上面打包動作
覆蓋fmt.phar，即可完成
fmt_smt.php修改部分
```php
            if ('true' === $lcText || 'false' === $lcText || 'null' === $lcText) {
                $lcText = strtoupper($text);
            }

			if (
				(
					('TRUE' === $lcText || 'FALSE' === $lcText || 'NULL' === $lcText) &&
					!$this->leftUsefulTokenIs([
```

很麻煩的話，可以下載我改過的:D
打包完的phar檔案需要的話可以自取
{% asset_link fmt.phar fmt.phar下載 %}

最後設定檔下面可以拿掉
```
  "phpfmt.psr2": false,
```

不過psr2除了小寫，不知道還有做什麼事情= =a