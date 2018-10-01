---
title: 'php字串"符號帶變數用法'
date: 2018-07-04 22:33:33
tags: [PHP,String,HEREDOC,NOWDOC]
categories: PHP
---


高職我第一翻開的書是PHP
從以前我就知道'&"在PHP的差別
但我沒有深入了解.....
今天踩到一個雷
`A PHP Error was encountered Severity: Notice Message: Undefined variable: date`
我發現我字串這樣寫
```php
<?php
$date = date("Y-m-d");
echo "$date之後要做什麼";
```
其實後來發現$date後面要空白，不能連在一起
同一個雷，不希望一直踩到
就筆記筆記吧

<!--more-->

下面是官網範例，簡單講""包起來遇到$就會抓取變數
不管是陣列還是物件都可以抓到
但注意$var後面字串要分格空白
**當然也可以用{}包起來，但要相連在一起**
```php
<?php
// 顯示所有錯誤
error_reporting(E_ALL);

$great = 'fantastic';

// 無效，輸出: This is { fantastic}
echo "This is { $great}";

// 有效，輸出： This is fantastic
echo "This is {$great}";
echo "This is ${great}";

// 有效
echo "This square is {$square->width}00 centimeters broad."; 

$juices = array("apple", "orange", "koolaid1" => "purple");

echo "He drank some $juices[0] juice.".PHP_EOL;
echo "He drank some $juices[1] juice.".PHP_EOL;
echo "He drank some juice made of $juice[0]s.".PHP_EOL; // Won't work
echo "He drank some $juices[koolaid1] juice.".PHP_EOL;

class people {
    public $john = "John Smith";
    public $jane = "Jane Smith";
    public $robert = "Robert Paulsen";
    
    public $smith = "Smith";
}

$people = new people();

echo "$people->john drank some $juices[0] juice.".PHP_EOL;
echo "$people->john then said hello to $people->jane.".PHP_EOL;
echo "$people->john's wife greeted $people->robert.".PHP_EOL;
echo "$people->robert greeted the two $people->smiths."; // Won't work  多加s   害我一直再看哪裡有問題orz

/*
 * 
 * 下面特殊狀況
 * 
 */ 
 //有效
echo "This square is {$square->width}00 centimeters broad."; 

// 有效，只有通過花括號語法才能正確解析帶引號的鍵名
// 這個雷我可能還不知道

echo "This works: {$arr['key']}";

// 有效
echo "This works: {$arr[4][3]}";

//沒有帶key的字串可以不用{} ex:$arr[4][3]

// 這是錯誤的表達式，因為就像 $foo[bar] 的格式在字符串以外也是錯的一樣。
// 換句話說，只有在 PHP 能找到常量 foo 的前提下才會正常工作；這裡會產生一個
// E_NOTICE (undefined constant) 級別的錯誤。
echo "This is wrong: {$arr[foo][3]}"; 

// 有效，當在字符串中使用多重數組時，一定要用括號將它括起來
echo "This works: {$arr['foo'][3]}";

// 有效
echo "This works: " . $arr['foo'][3];

echo "This works too: {$obj->values[3]->name}";

/**
 * 下面很神奇，可以用function return 字串出來，但不能直接用function回傳字串
 * 感覺不是很實用...
 */

echo "This is the value of the var named $name: {${$name}}";

echo "This is the value of the var named by the return value of getName(): {${getName()}}";

echo "This is the value of the var named by the return value of \$object->getName(): {${$object->getName()}}";

// 無效，輸出： This is the return value of getName(): {getName()}
echo "This is the return value of getName(): {getName()}";
```


> 函數、方法、靜態類變量和類常量只有在 PHP 5 以後才可在 {$} 中使用。然而，只有在該字符串被定義的命名空間中才可以將其值作為變量名來訪問。只單一使用花括號 ({}) 無法處理從函數或方法的返回值或者類常量以及類靜態變量的值。
```php
<?php
// 顯示所有錯誤
error_reporting(E_ALL);

class beers {
    const softdrink = 'rootbeer';
    public static $ale = 'ipa';
}

$rootbeer = 'A & W';
$ipa = 'Alexander Keith\'s';

// 有效，輸出： I'd like an A & W
echo "I'd like an {${beers::softdrink}}\n";

// 也有效，輸出： I'd like an Alexander Keith's
echo "I'd like an {${beers::$ale}}\n";

?>
```

以前我記得PHP有一個HEREDOC
但剛剛看文件5.3以後有一個NOWDOC

 heredoc 語法結構
nowdoc 語法結構（自 PHP 5.3.0 起） 

heredoc可以用$var 帶變數
但是nowdoc不會帶入$var變數
nowdoc用在XML會比較不會有問題

官網範例
```php

<?php
$str = <<<'EOD'
Example of string
spanning multiple lines
using nowdoc syntax.
EOD;

/* 含有变量的更复杂的示例 */
class foo
{
    public $foo;
    public $bar;

    function foo()
    {
        $this->foo = 'Foo';
        $this->bar = array('Bar1', 'Bar2', 'Bar3');
    }
}

$foo = new foo();
$name = 'MyName';

echo <<<'EOT'
My name is "$name". I am printing some $foo->foo.
Now, I am printing some {$foo->bar[1]}.
This should not print a capital 'A': \x41
EOT;
?>

```


### echo boolean 沒有東西原因

[PHP: String 字符串 - Manual](http://php.net/manual/zh/language.types.string.php#language.types.string.casting)

之前不知道為什麼echo boolean沒有出來東西
現在知道為什麼了


> 一個布爾值 boolean 的 TRUE 被轉換成 string 的 "1"。Boolean 的 FALSE 被轉換成 ""（空字符串）。這種轉換可以在 boolean 和 string 之間相互進行。
一個整數 integer 或浮點數 float 被轉換為數字的字面樣式的 string（包括 float 中的指數部分）。使用指數計數法的浮點數（4.1E+6
> 數組 array 總是轉換成字符串 "Array"，因此，echo 和 print 無法顯示出該數組的內容。要顯示某個單元，可以用 echo $arr['foo'] 這種結構。要顯示整個數組內容見下文。
在 PHP 4 中對象 object 總是被轉換成字符串 "Object"，如果為了調試原因需要打印出對象的值，請繼續閱讀下文。為了得到對象的類的名稱，可以用 get_class() 函數。自 PHP 5 起，適當時可以用 __toString 方法。
資源 resource 總會被轉變成 "Resource id #1" 這種結構的字符串，其中的 1 是 PHP 在運行時分配給該 resource 的唯一值。不要依賴此結構，可能會有變更。要得到一個 resource 的類型，可以用函數 get_resource_type()。
NULL 總是被轉變成空字符串。
如上面所說的，直接把 array，object 或 resource 轉換成 string 不會得到除了其類型之外的任何有用信息。可以使用函數 print_r() 和 var_dump() 列出這些類型的內容。 


[PHP: String 字符串 - Manual](http://php.net/manual/zh/language.types.string.php#language.types.string.casting)

看來有空來補這個筆記
[PHP: 類型轉換的判別 - Manual](http://php.net/manual/zh/language.types.type-juggling.php)

還發現PHP String有長度限制`2GB`

結果原本要記錄一個東西，越記越多XD
發現很多書都沒有教很清楚orz




