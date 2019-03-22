---
title: PHP - static那些事
date: 2019-02-20 22:17:56
tags: [php, static]
categories: PHP
---

最近使用在看`static`部份
平常很少用，過很久可能就會忘了這是什麼東西
但 global 部份應該不再記錄
[PHP 有關參照(Reference)的那些事 | 程式狂想筆記](https://malagege.github.io/blog/2019/01/28/PHP%E6%9C%89%E9%97%9C%E5%8F%83%E7%85%A7-Reference-%E7%9A%84%E9%82%A3%E4%BA%9B%E4%BA%8B/)
這裡面有簡單提到

<!--more-->

## static 用在變數

static 通常是寫在 function 裡面
但寫在 function 外面也不會報錯

一般沒有 static

```php
<?php
function test()
{
    $a = 0;
    echo $a;
    $a++;
}
test(); //0
test(); //0
test(); //0
?>
```

static 後

```php
<?php
function test()
{
    static $a = 0;
    echo $a;
    $a++;
}
test(); //1
test(); //2
test(); //3
?>
```

### 遞迴 function static 變數

先看官網範例，可以用在遞迴，但後面注意`$count--`的關係
所以跑完`$count`變數會歸 0
但是沒有寫`$count--`不會

```php

<?php
function test()
{
    static $count = 0;

    $count++;
    echo $count;
    if ($count < 10) {
        test();
    }
    $count--;
}


test(); // 12345678910
echo PHP_EOL;
test(); // 12345678910
```

### 定義 static 變數限制

簡單看官往範例，其實也滿好懂得
用運算式再次進入 function 怎麼知道初始值

```php
<?php
function foo(){
    static $int = 0;          // correct
    static $int = 1+2;        // correct (as of PHP 5.6)
    static $int = sqrt(121);  // wrong  (as it is a function)

    $int++;
    echo $int;
}
?>

```

靜態聲明是在編譯時解析的。
[深入理解 PHP 代码的执行的过程 - risingsun001 的专栏 - CSDN 博客](https://blog.csdn.net/risingsun001/article/details/22888861)
這一快我不是很了解

## static 用在 Object Function

### self

這邊先介紹 Object 會用到的 self

self:: 或者 \_\_CLASS\_\_，也可以用 get_class()呼叫
簡單說明一下，這邊跟 Java 的 this 很像
當是參照當前放置的 class 位置
可以看一下[java method 類別成員(class member) 繼承踩雷 | 程式狂想筆記](https://malagege.github.io/blog/2018/08/14/java-method-%E9%A1%9E%E5%88%A5%E6%88%90%E5%93%A1-class-member-%E7%B9%BC%E6%89%BF%E8%B8%A9%E9%9B%B7/)

```php

<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        self::who();
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();  #A
?>
```

### static

## Late Static Bindings(后期静态绑定)

static 跟 get_called_class()方法一樣，他是指實例後物件位置

```php

<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        static::who(); // 后期静态绑定从这里开始
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test(); # B
?>
```

> 在非靜態環境下，所調用的類即為該對象實例所屬的類。由於 \$this-> 會在同一作用範圍內嘗試調用私有方法，而 static:: 則可能給出不同結果。另一個區別是 static:: 只能用於靜態屬性。

### 在非靜態環境下使用 static::

> Note:
> 在非靜態環境下，所調用的類即為該對象實例所屬的類。由於 \$this-> 會在同一作用範圍內嘗試調用私有方法，而 static:: 則可能給出不同結果。另一個區別是 static:: 只能用於靜態屬性。

事後我在網路上找到了，更詳細解說
[PHP 中 self、static、\$this 的区别 & 后期静态绑定详解 - 彳亍 - CSDN 博客](https://blog.csdn.net/lamp_yang_3533/article/details/79912453)
{% asset_link 1.png 備份圖 %}
老實說這篇寫很亂了 orz

```
注意事项
非静态环境下的私有方法的查找顺序

在非静态环境下，在类的非静态方法中，使用 $this 和 static 调用类的私有方法时，执行方式有所不同。

    $this 会优先寻找所在定义范围（父类）中的私有方法，如果存在就调用。
    static 是先到它指向的类（子类）中寻找私有方法，如果找到了就会报错，因为私有方法只能在它所定义的类内部调用；如果没找到，再去所在定义范围（父类）中寻找该私有方法，如果存在就调用。

具体来说，$this 会先到所在定义范围内寻找私有方法，再到它指向的对象所属的类中寻找私有方法，然后寻找公有方法，最后到所在定义范围内寻找公共方法。只要找到了匹配的方法，就调用，并停止查找。

而 static 则是先到它指向的类中寻找私有方法，再寻找共有方法；然后到所在定义范围内寻找私有方法，再寻找共有方法。只要找到了匹配的方法，就调用，并停止查找。
---------------------
作者：lamp_yang_3533
来源：CSDN
原文：https://blog.csdn.net/lamp_yang_3533/article/details/79912453
版权声明：本文为博主原创文章，转载请附上博文链接！
```

```php

<?php
class A {
    private function foo() {
        echo "success!\n";
    }
    public function test() {
        $this->foo();
        static::foo(); //** C實例呼叫的時候會有問題
    }
}

class B extends A {
   /* foo() will be copied to B, hence its scope will still be A and
    * the call be successful */
}

class C extends A {
    private function foo() {
        /* original method is replaced; the scope of the new one is C */
    }
}

$b = new B();
$b->test();
$c = new C();
$c->test();   //fails

// success!
// success!
// success!
// <br />
// <b>Fatal error</b>:  Uncaught Error: Call to private method C::foo() from context 'A' in [...][...]:9
// Stack trace:
// #0 [...][...](27): A-&gt;test()
// #1 {main}
//   thrown in <b>[...][...]</b> on line <b>9</b><br />


// PHP 5
// Fatal error:  Call to private method C::foo() from context 'A' in /tmp/test.php on line 9
?>


```

> Late static bindings' resolution will stop at a fully resolved static call with no fallback. On the other hand, static calls using keywords like parent:: or self:: will forward the calling information.

```php
<?php
class A {
    public static function foo() {
        static::who();// (2) 遇到後期靜態綁定
    }

    public static function who() {
        echo __CLASS__."\n";
    }
}

class B extends A {
    public static function test() {
        A::foo();// 這不是轉發 (1)
        parent::foo();// 這是轉發 (1)
        self::foo();//   這是轉發 (1)
        static::foo();// 這是轉發 (1)
    }

    public static function who() {
        echo __CLASS__."\n";
    }
}
class C extends B {
    public static function who() {
        echo __CLASS__."\n";
    }
}

C::test();
// A
// C
// C
// C

// A
// C
// C
// C
?>
```

```php
<?php
class A {
    public static function foo() {
        static::who();//
    }

    public static function who() {
        echo __CLASS__."\n";  //這個不是後期靜態綁定
    }
}

class B extends A {
    public static function test() {
        A::who();// 這不是轉發
        parent::who();// 這是轉發
        self::who();// 這是轉發
        static::who();// 這是轉發
    }

    public static function who() {
        echo __CLASS__."\n";
    }
}
class C extends B {
    public static function who() {
        echo __CLASS__."\n";
    }
}
$c = new C;
$c->test();
C::test();
// A
// A
// B
// C

// A
// A
// B
// C
?>
```

老實說上面例子花我很多時間想的，重寫兩次文章(因為一直發現理解錯誤)
只要進入 B 的 parent,static,self 再跳到 A 的 test function 裡面的`static::who();`(後期靜態綁定)
就會抓當下 class
不管是`實例`或者 `static`
都會印出 C

---

下面是直接在 B 的 test()做，第一次的時候做轉發，跳到下一個 function \*_沒有_`用後期靜態綁定`
而且\_\_CLASS\_\_(self)，只會抓到原本的類別(非實例出來的類別)

這邊的 static 轉發(forward)同等於`forward_static_call()`
所以下面這個範例，\_\_CLASS\_\_改成`get_called_class()`一樣會變成上面結果一致

- [PHP: forward_static_call - Manual](http://php.net/manual/zh/function.forward-static-call.php)

## forward_static_call 和 call_user_func

這兩個 function 功能很像，但差在用在 static 地方。
重點是下面這兩個`call_user_func('A::bar'); //non-forwarding, 'A'`和`forward_static_call('A::bar'); //forwarding, 'B'`
這兩個讓我想到 JavaScript 閉包用到(this)這個東西，但 js 解決方法是用`箭頭函式`和`var that = this`解決(題外話，請忽略這段)

但 forward_static_call 有一個例外，當父類別到子類別的話，不做轉發動做，看下面範例

[php 5.3 - PHP forward_static_call vs call_user_func - Stack Overflow](https://stackoverflow.com/questions/5061476/php-forward-static-call-vs-call-user-func)

```
The difference is just that forward_static_call does not reset the "called class" information if going up the class hierarchy and explicitly naming a class, whereas call_user_func resets the information in those circumstances (but still does not reset it if using parent, static or self).

Example:

<?php
class A {
    static function bar() { echo get_called_class(), "\n"; }
}
class B extends A {
    static function foo() {
        parent::bar(); //forwards static info, 'B'
        call_user_func('parent::bar'); //forwarding, 'B'
        call_user_func('static::bar'); //forwarding, 'B'
        call_user_func('A::bar'); //non-forwarding, 'A'
        forward_static_call('parent::bar'); //forwarding, 'B'
        forward_static_call('A::bar'); //forwarding, 'B'
    }
}
B::foo();

Note that forward_static_call refuses to forward if going down the class hierarchy:

<?php
class A {
    static function foo() {
        forward_static_call('B::bar'); //non-forwarding, 'B'
    }
}
class B extends A {
    static function bar() { echo get_called_class(), "\n"; }
}
A::foo();

Finally, note that forward_static_call can only be called from within a class method.

```

The difference is just that forward_static_call does not reset the "called class" information if going up the class hierarchy and explicitly naming a class, whereas call_user_func resets the information in those circumstances (but still does not reset it if using parent, static or self).

最後我覺得 static 和\$this 很相似
但看到這篇裡面有寫差異
雖然我有一點 Java 物件導向觀念，但是 PHP 物件導向還滿難理解

```
static 和 $this 有点类似，但又有区别：

    $this 指向的对象所属的类和 static 指向的类相同。
    $this 不能用于静态方法中，也不能访问类的静态属性和常量。
    $this 指向的是实际调用的对象。
    static 可以用于静态或非静态方法中，也可以访问类的静态属性、静态方法、常量和非静态方法，但不能访问非静态属性。
    static 指向的是实际调用时的对象所属的类。
---------------------
作者：lamp_yang_3533
来源：CSDN
原文：https://blog.csdn.net/lamp_yang_3533/article/details/79912453
版权声明：本文为博主原创文章，转载请附上博文链接！
```

## 參考來源

- [PHP 的 static 與 self 兩個關鍵字的差異 | Asika Lab 飛鳥實驗室](http://asika.windspeaker.co/post/3483-php-static-self)
- [PHP: Late Static Bindings - Manual](http://php.net/manual/en/language.oop5.late-static-bindings.php)
- [PHP: forward_static_call - Manual](http://php.net/manual/zh/function.forward-static-call.php)
- [PHP forward_static_call vs call_user_func | landcareweb.com](http://landcareweb.com/questions/30864/php-forward-static-call-vs-call-user-func)
- [PHP 5.3/6 新增功能 - Late Static Bindings - 石頭閒語](http://rocksaying.tw/archives/10796755.html)
- [php 5.3 - PHP forward_static_call vs call_user_func - Stack Overflow](https://stackoverflow.com/questions/5061476/php-forward-static-call-vs-call-user-func)
- [PHP forward_static_call 和 call_user_func - 代码日志](https://codeday.me/bug/20181023/313202.html)
- [PHP: forward_static_call - Manual](http://php.net/manual/zh/function.forward-static-call.php)
- [PHP 关于 self:: 和 static:: | 随意博客](http://www.cleey.com/blog/single/id/854.html)
- [通过 static 关键词来实现 late static binding（静态调用绑定）-pilishen.com,做全球最好的实战教程](http://www.pilishen.com/posts/php-late-static-bindings-in-laravel)
- [[PHP 觀念]self 和\$this 的差異 @ 麥克的學習紀錄 :: 痞客邦 ::](http://miggo.pixnet.net/blog/post/30799978-%5Bphp%E8%A7%80%E5%BF%B5%5Dself%E5%92%8C%24this%E7%9A%84%E5%B7%AE%E7%95%B0)
