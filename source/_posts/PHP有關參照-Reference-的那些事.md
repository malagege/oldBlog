---
title: PHP有關參照(Reference)的那些事
date: 2019-01-28 21:46:56
tags: [php]
categories: PHP
---

最近在看 PHP 官方參考文件
一直以為 PHP 有一個指針(pointer)觀念
今天讀到官網說「PHP 不是 reference、也不是 pinter 觀念」(雖然這樣想幾乎 87%都不會有問題)
瞬間有點黑人問號臉
也許是剛學時候就用這樣記住
但是今天要好好重新認識 PHP Reference
聽說就跟 Linux Hard Link 一樣的道理

<!--more-->

## PHP 參照不是指針(pointer)

[PHP: 引用是什么 - Manual](http://php.net/manual/zh/language.references.whatare.php)

```
在 PHP 中引用意味著用不同的名字訪問同一個變量內容。這並不像 C 的指針：例如你不能對他們做指針運算，他們並不是實際的內存地址…… 查看引用不是什麼瞭解更多信息。 替代的是，引用是符號表別名。注意在PHP 中，變量名和變量內容是不一樣的， 因此同樣的內容可以有不同的名字。最接近的比喻是 Unix 的文件名和文件本身——變量名是目錄條目，而變量內容則是文件本身。引用可以被看作是 Unix 文件系統中的硬鏈接。
```

這樣看真的沒有懂

直接看下面的*User Contributed Notes*人家給的範例

```php
<?php
var = "foo";
$ref1 =& $var; // new object that references $var
$ref2 =& $ref1; // references $var directly, not $ref1!!!!!

echo $ref1; // >foo

unset($ref1);

echo $ref1; // >Notice:  Undefined variable: ref1
echo $ref2; // >foo
echo $var; // >foo
?>
```

其實這個範例可以很簡單看出來，刪除`unset` $ref1變數不會影響到$ref2
這個我記得在 Java 會直接 byebye
~~JavaScript 發現不能用 delete object，看來要找天測試是為什麼~~[delete operator | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/delete)

再看[PHP: 引用做什么 - Manual](http://php.net/manual/zh/language.references.whatdo.php)

```php
<?php
$a =& $b;
?>
```

**Note:**

- $a 和 $b 在這裡是完全相同的，這並不是 $a 指向了 $b 或者相反，而是 $a 和 $b 指向了同一個地方。
- 如果具有引用的數組被拷貝，其值不會解除引用。對於數組傳值給函數也是如此。

## 參照

```php
<?php

$a = 1;
$b = &$a;
$b++;
$c = $b;
$a = 0;
var_dump($c);   #2
```

```php
<?php

$a = 1;
$b = &$a;
$b++;
$c = &$b;
$a = 0;
var_dump($c);   #0
```

## 官網神奇的範例...

如果對一個未定義的變量進行引用賦值、引用參數傳遞或引用返回，則會自動創建該變量。
這一段真的很神奇...，不過應該是 function 會宣告上面的變數。

```php
<?php
function foo(&$var) { }

foo($a); // $a is "created" and assigned to null

$b = array();
foo($b['b']);
var_dump(array_key_exists('b', $b)); // bool(true)

$c = new StdClass;
foo($c->d);
var_dump(property_exists($c, 'd')); // bool(true)
?>
```

## 官方下面的範例

**_以下有毒，假如理解不能請不要再深入，有可能會走火入魔_**
**_以下有毒，假如理解不能請不要再深入，有可能會走火入魔_**
**_以下有毒，假如理解不能請不要再深入，有可能會走火入魔_**

目前簡單用我想的方法
`沒有用&`當做`copy覆蓋到當前(箭頭)位置的值`
`用&`當作`這個變數(箭頭)重新指向到某一個位置`(跟 linux hard link 一樣)
以下注解是我用自已方法去記錄的，有錯誤也請不要見怪

```php
<?php
//if you do:

$a = "hihaha";
$b = &$a;
$c = "eita";
$b = $c;
echo $a; // shows "eita"

# $b指向為$a位置，$b的位置改當然$a也會跟著換

$a = "hihaha";
$b = &$a;
$c = "eita";
$b = &$c;
echo $a; // shows "hihaha"

#  **這邊是重點**
#  $b範例都是參照位置
#  其實很容易被Java Reference帶著走

$a = "hihaha";
$b = &$a;
$b = null;
echo $a; // shows nothing (both are set to null)

$a = "hihaha";
$b = &$a;
unset($b);
echo $a; // shows "hihaha"
?>
```

2019-02-20 簡單小記
`$b`值為=右邊變數的值(先不管有沒有&)
當然不一樣的結果在於前面的`$b=&`

## 用自已的方法手畫上面前四個範例

{% asset_img 1-1.png 範例1 %}
{% asset_img 1-2.png 範例2 %}
{% asset_img 1-3.png 範例3 %}
{% asset_img 1-4.png 範例4 %}

簡單來說，我有一個方法不會搞混
首先，分兩部份
第一部份是 變數名字
第二部份是 記錄框框資料
兩個部份會有一個箭頭做關連

```php
<?php
$a = 1;
$b = $a;
```

平常給值是
{% asset_img 1-5.png 範例5 %}

## PHP5 new 物件預設是參照

`自 PHP 5 起，new 自动返回引用，因此在此使用 =& 已经过时了并且会产生 E_STRICT 级别的消息。`

## Global 變數使用參照

百思不得其解的範例，官方有說明`global $var; 是 $var =& $GLOBALS['var']; 的簡寫。`
但要熟悉上面規則，就能理解下方程式為什麼不會影響外面的`$var2`(全域變數)
這可能沒有深入了解 PHP 參照，可能會很難理解
下範例外面的 Global 使用參照，卻不會影響全域變數

```php
<?php
$var1 = "Example variable";
$var2 = "";

function global_references($use_globals)
{
    global $var1, $var2;
    if (!$use_globals) {
        $var2 =& $var1; // visible only inside the function
        echo 'var2:'  . $var2 . ',' . $GLOBALS["var2"]. PHP_EOL;
    } else {
        $GLOBALS["var2"] =& $var1; // visible also in global context
    }
}

global_references(false);
echo "var2 is set to '$var2'\n"; // var2 is set to ''
global_references(true);
echo "var2 is set to '$var2'\n"; // var2 is set to 'Example variable'
?>

```

這個範例其實跟上一個例子一樣
其實他是參照到別的地方

話說我剛好也不是很了解 php global
趁這個機會了解

[PHP: What References Are Not - Manual](http://php.net/manual/en/language.references.arent.php)
原 function 的$var是參照$bar
但是$var =& $GLOBALS["baz"];
簡單來說

```php
<?php
function foo(&$var)
{
    $var =& $GLOBALS["baz"];
}
foo($bar);
?>

```

這邊用 global 感覺容易混淆

```php
<?php
        //Enter your code here, enjoy!
$bar = 1;
function foo(&$var)
{
    $a = 2;
    $var =& $a;
}
foo($bar);
echo $bar;  #1
```

感覺`foo(&$var)`使用上等於`區域變數 $var = & 全域變數 $bar`($var = &$GLOBAL['bar'])
所以到最後重新指向位置並不會影響\$bar

## Returning References

```php

<?php
class foo {
    public $value = 42;

    public function &getValue() {
        return $this->value;
    }
}

$obj = new foo;
$myValue = &$obj->getValue(); // $myValue is a reference to $obj->value, which is 42.
$obj->value = 2;
echo $myValue;                // prints the new value of $obj->value, i.e. 2.
?>
```

簡單來說，Returning References 用在物件導向需要兩個`&`

最近看 Codeigiter 原程式有用`function &xxxx`
一直在想這個為什麼要這樣用...
在想是不是之後會帶入到 function，防止不是參照(以下自己猜測，但後來想想好像不是，看看就好= =a)
讓 functiont 產生`Only variables should be passed by reference`(詳細請看下面內容)
不過有在官網看到[PHP: 變量範圍 - Manual](http://php.net/manual/zh/language.variables.scope.php#language.variables.scope.static)
目前還不確定是不是這個關係

> 在 Zend 引擎 1 代，它驅動了 PHP4，對於變量的 static 和 global 定義是以引用的方式實現的。例如，在一個函數域內部用 global 語句導入的一個真正的全局變量實際上是建立了一個到全局變量的引用。這有可能導致預料之外的行為，如以下例子所演示的：

```php
// 類似的行為也適用於 static 語句。引用並不是靜態地存儲的：
//以下code為php4
<?php
function &get_instance_ref() {
    static $obj;

    echo 'Static object: ';
    var_dump($obj);
    if (!isset($obj)) {
        // 將一個引用賦值給靜態變量
        $obj = &new stdclass;
    }
    $obj->property++;
    return $obj;
}

function &get_instance_noref() {
    static $obj;

    echo 'Static object: ';
    var_dump($obj);
    if (!isset($obj)) {
        // 將一個對象賦值給靜態變量
        $obj = new stdclass;
    }
    $obj->property++;
    return $obj;
}

$obj1 = get_instance_ref();
$still_obj1 = get_instance_ref();
echo "\n";
$obj2 = get_instance_noref();
$still_obj2 = get_instance_noref();
?>
```

官網還有提到一個可能會遇到的錯誤問題

- [[PHP]Only variables should be passed by reference - 王永濤的个人页面 - 开源中国](https://my.oschina.net/wangyongtao/blog/531812)
- [PHP: Only variables should be passed by reference – James' Desk](https://vijayasankarn.wordpress.com/2017/08/28/php-only-variables-should-be-passed-by-reference/)

首先先提提`array_push(array(), 'foo');`，程式會顯示錯誤，原因需要這個變數被實例

```php
<?php
function &collector() {
  static $collection = array();
  return $collection;
}
array_push(collector(), 'foo');
?>
```

假如

```php
<?php
function collector() {
  static $collection = array();
  return $collection;
}
array_push(collector(), 'foo');
?>
```

程式會顯示 `Only variables should be passed by reference`

## Passing by Reference

```php

<?php
function foo(&$var)
{
    $var++;
}

$a=5;
foo($a);
// $a is 6 here
?>
```

官網有給之前提過的錯誤，`Only variables should be passed by reference`問題

```php

<?php
function foo(&$var)
{
    $var++;
}
function bar() // Note the missing &
{
    $a = 5;
    return $a;
}
foo(bar()); // Produces fatal error as of PHP 5.0.5, strict standards notice
            // as of PHP 5.1.1, and notice as of PHP 7.0.0

foo($a = 5); // Expression, not variable
foo(5); // Produces fatal error

class Foobar
{
}

foo(new Foobar()) // Produces a notice as of PHP 7.0.7
                  // Notice: Only variables should be passed by reference
?>
```

## Unsetting References

```php
<?php
$a = 1;
$b =& $a;
unset($a);
var_dump($a); # null
var_dump($b); # 1
?>
```

> won't unset $b, just $a.
> Again, it might be useful to think about this as analogous to the Unix **unlink** call.

用 Linux 的 link hard 觀念確實方便理解

## Spotting References

### global References

> When you declare a variable as global \$var you are in fact creating reference to a global variable. That means, this is the same as:

```php
<?php
$var =& $GLOBALS["var"];
?>
```

> This also means that unsetting \$var won't unset the global variable.

意思是 unset 變數也不會影響到全域變數

### \$this

In an object method, \$this is always a reference to the called object.

\$this 參照呼叫實例物件

## 心得

費很多功夫整理這個，光吸收可能需要花費一堆時間
因為想進入 PHP 領域，了解這個應該也是必經之道
平常也很少機會用`&`這玩意吧
有空整理一下 PHP 物件導向觀念。
