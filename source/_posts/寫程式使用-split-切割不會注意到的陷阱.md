---
title: 寫程式使用 split 切割不會注意到的陷阱
date: 2020-10-28 22:42:19
tags: [linux, split]
categories: Linux
---

最近 JAVA 程式遇到一個奇怪錯誤
使用 split 針對 data 做分割
切割符號為 ^
最後發現是後面空白會合併空白

<!--more-->


```java
public class MyClass {
    public static void main(String args[]) {
        String str = new String("1^^2^^^3^4^5^^^^");
 
        System.out.println("^ 分隔符返回值 :" );
        int index = 0;
        for (String retval: str.split("\\^")){
            System.out.println((index++) + ":" + retval);
        }
 
    }
}
```

得到結果會是

```
^ 分隔符返回值 :
0:1
1:
2:2
3:
4:
5:3
6:4
7:5
```

後面的^資料都是空白的話，都不會在陣列

我一直以為別的程式語言也是這樣

## JavaScript

```javascript
"1^2^3^4^5^^^^".split('^')
// Array(9) [ "1", "2", "3", "4", "5", "", "", "", "" ]
```

等一下，Java 這邊好像是用 Regex
馬上找一下方法嘗試

```javascript
"1^2^3^4^5^^^^".split(/\^/)
// Array(9) [ "1", "2", "3", "4", "5", "", "", "", "" ]
```

結果還是一樣

## PHP


```php
var_dump(explode("^","1^2^3^4^5^^^^"));
```


```php
var_dump( preg_split("/\\^/", "1^2^3^4^5^^^^"));
```

都是

```
array(9) {
  [0]=>
  string(1) "1"
  [1]=>
  string(1) "2"
  [2]=>
  string(1) "3"
  [3]=>
  string(1) "4"
  [4]=>
  string(1) "5"
  [5]=>
  string(0) ""
  [6]=>
  string(0) ""
  [7]=>
  string(0) ""
  [8]=>
  string(0) ""
}

```

## 結果

Java 處理 Split 機制不太一樣
這邊可以看看 split 可以帶第二個參數

[String (Java Platform SE 7 )](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#split(java.lang.String,%20int))


### String.split()與com.sun.deploy.util.StringUtils.split()的區別

[String.split()与StringUtils.split()的区别_卡卡_西瓜的博客-CSDN博客](https://blog.csdn.net/csdm_admin/article/details/68483417)

```java
import com.sun.deploy.util.StringUtils;
String s =",1,,2,3,4,,";
String[] split1 = s.split(",");
// 
String[] split2 = StringUtils.splitString(s, ",");
// 1,2,3,4
```

apache 結果會跟下面一樣


#### 找到我們想要的

org.apache.commons.lang3.StringUtils#splitPreserveAllTokens(str, separatorChar)
[StringUtils (Apache Commons Lang 3.11 API)](http://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/StringUtils.html#splitPreserveAllTokens-java.lang.String-char-)

org.apache.commons.lang3.StringUtils#splitByWholeSeparatorPreserveAllTokens(str, separator)

歡呼!!
[Java 有沒有像 Python 一樣的 split？ - 摸鱼](https://www.mofish.work/thread/4471)
看來還滿多人苦惱這個

### 詳細解說

有爬到這篇[Java 字符串 split 踩坑记](https://juejin.im/post/6844903966392713224)

 com.google.common.base.Splitter 這個比較是接近我們想要的

 