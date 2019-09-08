---
title: PHP return 特別的用法小記
date: 2019-09-08 17:27:22
tags: [return]
categories: PHP
---

今天看到我同事用 `return` 寫在 xxx.php(沒有寫在 return 裡面)
我印象中 return 通常都寫在 function 裡面
查看了一下官方文件 [PHP: return - Manual](https://www.php.net/manual/zh/function.return.php)
可以寫在 function 外面

<!--more-->

## 先講講單一文件

文件有講到 return 不是一個 function
先講講這個跟 `die/exit`哪裡不一樣

```php
<?php
return 2;
```

```php
exit(2);
```

要怎麼看錯誤碼，我以前這篇寫過
[linux 設定環境變數 | 程式狂想筆記](https://malagege.github.io/blog/2018/08/09/linux-%E8%A8%AD%E5%AE%9A%E7%92%B0%E5%A2%83%E8%AE%8A%E6%95%B8/)

> \$?是指令回傳錯誤碼

## 有做 include 文件

> 如果在全局範圍中調用，則當前腳本文件中止運行。如果當前腳本文件是被 include 的或者 require 的，則控制交回調用文件。此外，如果當前腳本是被 include 的，則 return 的值會被當作 include 調用的返回值。如果在主腳本文件中調用 return，則腳本中止運行。如果當前腳本文件是在 php.ini 中的配置選項 auto_prepend_file 或者 auto_append_file 所指定的，則此腳本文件中止運行。

我覺得先看範例

```php
a.php
<?php
include("b.php");
echo "a";
?>
```

b.php

```php
<?php
echo "b";
return;
?>
```

(executing a.php:) will echo "ba".

whereas (b.php modified):

a.php

```php
<?php
include("b.php");
echo "a";
?>
```

b.php

```php
<?php
echo "b";
exit;
?>
```

(executing a.php:) will echo "b".

** 小心 global namespace **

a.php

```php
<?php
include 'b.php';

foo();
?>
```

b.php

```php
<?php
return;

function foo() {
     echo 'foo';
}
?>
```

Executing a.php will output "foo".

### include 和 return

[PHP: include - Manual](https://www.php.net/manual/zh/function.include.php)

```php
return.php
<?php

$var = 'PHP';

return $var;

?>
```

noreturn.php

```php
<?php

$var = 'PHP';

?>
```

testreturns.php

```php
<?php

$foo = include 'return.php';

echo $foo; // prints 'PHP'

$bar = include 'noreturn.php';

echo $bar; // prints 1

?>
```

> \$bar 的值為 1 是因為 include 成功運行了。注意以上例子中的區別。第一個在被包含的文件中用了 return 而另一個沒有。如果文件不能被包含，則返回 FALSE 並發出一個 E_WARNING 警告。

> 如果在包含文件中定義有函數，這些函數不管是在 return 之前還是之後定義的，都可以獨立在主文件中使用。如果文件被包含兩次，PHP 5 發出致命錯誤因為函數已經被定義，但是 PHP 4 不會對在 return 之後定義的函數報錯。推薦使用 include_once 而不是檢查文件是否已包含並在包含文件中有條件返回。

### include_once 和 return

[PHP: include_once - Manual](https://www.php.net/manual/en/function.include-once.php)
其實看範例很快就可以懂了
載入進去預設會為 True，假如文件有`return value`，include_once 就會回傳
但`include_once`第二次不會回傳東西

```php
config.php
<?php
return array("test">1);
```

---

//first

```php
$config = include_once("config.php");
var_dump($config);

$config = include_once("config.php");
var_dump($config);
```

---

output will be
array(
"test"=>1,
)

nothing

### 小記 include 和 require 差別

> require 和 include 的不同 require 適合用來引入靜態的內容，而 include 則適合用來引入動態的程式碼。
> include 在執行時，如果 include 進來的檔案發生錯誤的話，會顯示警告，不會立刻停止；
> 而 require 則是會顯示錯誤，立刻終止程式，不再往下執行。
> include 可以用在迴圈；require 不行。

參考來源: [Syun: [PHP]include 與 require 的差別](http://syunguo.blogspot.com/2013/04/phpinclude-require.html)
