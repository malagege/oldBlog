---
title: BigDecimal.intValue 超過 int 長度會變成負號
date: 2021-11-07 22:14:18
tags: [java,bigdecimal]
categories: Java
---

最近發現我的專案很多都用bigdecimal.intvalue()去做結果。結果有一筆99億額計算出了大問題。建議不要亂轉 intValue。


<!--more-->

[Java.math.BigDecimal.intValue()方法實例 - Java.math包](http://tw.gitbook.net/java/math/bigdecimal_intvalue.html)

```java=

import java.math.*;

public class BigDecimalDemo {

    public static void main(String[] args) {

        // create 3 BigDecimal objects
        BigDecimal bg1, bg2;

        //Create 2 int Object
        int i1, i2;

        bg1 = new BigDecimal("1234");

        //assign a larger value to bg2
        bg2 = new BigDecimal("3383878445");

        // assign the int value of bg1 and bg2 to i1,i2 respectively
        i1 = bg1.intValue();
        i2 = bg2.intValue();

	String str1 = "int value of " + bg1 + " is " + i1;
	String str2 = "int value of " + bg2 + " is " + i2;

        // print i1,i2
        System.out.println( str1 );
        System.out.println( str2 );
    }
}
```

![](https://i.imgur.com/D7e11oy.png)



## 解決方案

### 完美方案

` new BigDecimal("100.000").stripTrailingZeros().toPlainString()`

[bigdecimal去除末尾多餘的0 ,stripTrailingZeros()科學計數法解決_人生最遺憾的莫過於：輕易地放棄了不該放棄的,固執地堅持了不該堅持的-CSDN博客_striptrailingzeros](https://blog.csdn.net/xingxiupaioxue/article/details/78088337)
```
BigDecimal是處理高精度的浮點數運算的常用的一個類

當需要將BigDecimal中保存的浮點數值打印出來，特別是在頁面上顯示的時候，就有可能遇到預想之外的科學技術法表示的問題。

一般直接使用 BigDecimal.toString()方法即可以完成浮點數的打印。

如：

    System.out.println( new BigDecimal("10000000000").toString());

但是，toString()方法輸出的字符串並不能保證不是科學計數法。

不過在日常的使用中，用toString()方法輸出的就是普通的數字字符串而非科學計數法。

直接這麼寫：

    System.out.println( new BigDecimal("100.000").toString());

程序的輸出即為：  100.000

如果我們希望去除末尾多餘的0，那麼我們應該這麼寫：

    System.out.println( new BigDecimal("100.000").stripTrailingZeros().toString());

其中，stripTrailingZeros()函數就是用於去除末尾多餘的0的，但是此時程序的輸出為： 1E+2

是科學計數法，可能並不是我們想要的。

解決的方法很簡單，如果想要避免輸出科學計數法的字符串，我們要用toPlainString()函數代替toString()。如：

    System.out.println( new BigDecimal("100.000").stripTrailingZeros().toPlainString());

此時程序的輸出就為 100
————————————————
版权声明：本文为CSDN博主「树上的疯子^」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/xingxiupaioxue/article/details/78088337
```

相關文章
[BigDecimal類型去除多余的小數點和小數點後的0 - IT閱讀](https://www.itread01.com/content/1500636132.html)

## 別的方案


[BigDecimal截取整数位放弃小数位，不进位_雪落南城的博客-CSDN博客_bigdecimal截取整数](https://blog.csdn.net/lbh199466/article/details/100664670)

```
截取到整数位 可以先除1000保留到个位 然后再乘1000这样

例：

9.9 截取整数位后  9

代码：

BigDecimal c1 = BigDecimal.valueOf(99400).divide(BigDecimal.valueOf(10000), 0, BigDecimal.ROUND_DOWN);

或者

BigDecimal c2 = BigDecimal.valueOf(9.9).setScale(0, BigDecimal.ROUND_DOWN);

BigDecimal c3 = BigDecimal.valueOf(9.1).setScale(0, BigDecimal.ROUND_FLOOR);

BigDecimal c4 = BigDecimal.valueOf(9.9).setScale(0, BigDecimal.ROUND_FLOOR);

输出：都为9
————————————————
版权声明：本文为CSDN博主「雪落南城」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/lbh199466/article/details/100664670
```




## JDK7 BUG(雷)


- [java.math.BigDecimal.stripTrailingZeros 对0的处理时注意了 - 成诺 - 博客园](https://www.cnblogs.com/zhanhb/archive/2012/04/02/2429941.html)


- [java - represent BigDecimal without decimal part as integer - Stack Overflow](https://stackoverflow.com/questions/29207869/represent-bigdecimal-without-decimal-part-as-integer?rq=1)