---
title: java method 類別成員(class member) 繼承踩雷
date: 2018-08-14 22:36:52
tags: [java, php, JavaScript, class]
categories: 寫程式一些事
---

這篇先記錄最近改報表所踩到雷
一直以為我覺得 class member 也可以做 java Overwrite
但 overwrite 好像是指 method
在做繼承方發一想不到事情

<!--more-->

```java=
public class A{
    int usertype = 0;
    public void ok(){
        System.out.println(usertype);
    }
    public static void main(String args[]) {
        C c = new C();
        c.ok();
    }
}

class B extends A{
    // int usertype = 2;
    B(){
        usertype = 2;
    }
}

class C extends B{
    // int usertype = 3;
    C(){
        usertype = 3;
    }
}

```

結果印出應該是 3

但是把 usertype=2 跟 usertype=3 前面注解拿掉
印出來的東西會是`0`
這個雷...

做後原本想做 js 做做看

JS class 不能用 class member...
發現 typescript 也不行
可能真的在 class member 轉 function 行不通

又好奇試試 PHP
結果發現跟 JAVA 完全不一樣的結果

PHP

```php
<?php
class A{
    public $a = 1;
    public function ok(){
        echo $this->a;
    }
}

class B extends A{
    public $a =2;
}

class C extends B{
    public function __construct()
    {
        $a =3;
    }
}


$o = new C();
$o->ok();
//print 3
```

PHP class 比較像 JAVA，但結果不一樣...，我感到意外
不過...抽象類別 裡面方法不能用抽象 function
詳細[抽象類別（Abstract Class） · PHP 學習筆記](https://kejyuntw.gitbooks.io/php-learning-notes/class/abstract-class.html)

python,golang 我就不試了 orz

[D2:［Java］類別的繼承（基本範例解說） - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10184771)

### 2019-02-21

今天剛好做 PHP 有關 class，突然想到 Java 之前遇到的問題還沒有理解為什麼

感覺`this`是參照原本 method 那一層的(這一點跟 PHP self 很像，\$this 是實例物件)
所以一直抓到`父層 A`，沒有解決抓到在`C`
這解決方法除了寫在建構式，寫在子層`static`也能解決

> 我原先的錯誤想法
> 在想是因為 extends，所以父層 function 複製到子層那邊去
> 其實用參照(reference)的方法想就很好懂了

```java
class Son extends Dad {
    static {
       me = 'son';
    }
}
```

[inheritance - Is there a way to override class variables in Java? - Stack Overflow](https://stackoverflow.com/questions/685300/is-there-a-way-to-override-class-variables-in-java)

難怪最近看到有一個程式
宣告 class 裡面的變數
都不會直接給值，都寫在 `constructor`

也許子層不需要再重新宣告父層有的變數
複寫都在`constructor`可能會比較好
這可能是最好的良好習慣

參考來源:

- [關於 this](https://openhome.cc/Gossip/JavaGossip-V1/AboutThis.htm)
- [this 與 final](https://openhome.cc/Gossip/Java/Lambda-this-final.html)
- [通过 static 关键词来实现 late static binding（静态调用绑定）-pilishen.com,做全球最好的实战教程](http://www.pilishen.com/posts/php-late-static-bindings-in-laravel)
- [繼承](https://programming.im.ncnu.edu.tw/J_Chapter6.htm)
- [[java 程式筆記]建構子 Constructor - 觀念與例題研究 @ 貓羽的文字日誌 :: 痞客邦 ::](http://whitecat2.pixnet.net/blog/post/56375979-%5Bjava%E7%A8%8B%E5%BC%8F%E7%AD%86%E8%A8%98%5D%E5%BB%BA%E6%A7%8B%E5%AD%90constructor---%E8%A7%80%E5%BF%B5%E8%88%87%E4%BE%8B%E9%A1%8C%E7%A0%94)
- [inheritance - Accessing inherited class variables in java - Stack Overflow](https://stackoverflow.com/questions/8763271/accessing-inherited-class-variables-in-java)
- [JWorld@TW Java 論壇 - 建構子??](https://www.javaworld.com.tw/jute/post/view?bid=29&id=181644)
- [inheritance - Is there a way to override class variables in Java? - Stack Overflow](https://stackoverflow.com/questions/685300/is-there-a-way-to-override-class-variables-in-java)
- [override - Overriding member variables in Java - Stack Overflow](https://stackoverflow.com/questions/10722110/overriding-member-variables-in-java)
- [JAVA](http://www.codedata.com.tw/book/java-basic-source/ch11-3.htm)
- [[PHP 觀念]self 和\$this 的差異 @ 麥克的學習紀錄 :: 痞客邦 ::](http://miggo.pixnet.net/blog/post/30799978-%5Bphp%E8%A7%80%E5%BF%B5%5Dself%E5%92%8C%24this%E7%9A%84%E5%B7%AE%E7%95%B0)
