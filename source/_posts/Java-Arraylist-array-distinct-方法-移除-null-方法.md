---
title: Java Arraylist/array distinct 方法 / 移除 null 方法
date: 2022-07-31 02:09:58
tags: [java,array,arraylist,distinct]
categories: Java
---

整理網路文章方法小記。

<!--more-->

[list - Get unique values from ArrayList in Java - Stack Overflow](https://stackoverflow.com/questions/13429119/get-unique-values-from-arraylist-in-java)

```java=
List<String> gasList = // create list with duplicates...
Set<String> uniqueGas = new HashSet<String>(gasList);
System.out.println("Unique gas count: " + uniqueGas.size());

uniqueGas.toArray(new String[0]);
```

```java=
String[] unique = new HashSet<String>(Arrays.asList(array)).toArray(new String[0]);
and shorter and simpler in java 8:

String[] unique = Arrays.stream(array).distinct().toArray(String[]::new);
```

[java - Array of unique elements? - Stack Overflow](https://stackoverflow.com/questions/14656208/array-of-unique-elements)


```java=
int[] noDuplicates = IntStream.of(array).distinct().toArray();
```


[Java 8 stream distinct by multiple fields - HowToDoInJava](https://howtodoinjava.com/java8/stream-distinct-by-multiple-fields/)


## 移除 null 方法

Java program to remove nulls from a List Container

各種寫法...大神啦!!

[Java program to remove nulls from a List Container - GeeksforGeeks](https://www.geeksforgeeks.org/java-program-to-remove-nulls-from-a-list-container/)
[備份圖](https://imgur.com/a/N0StkEG)

```java=
import java.util.*;
  
class GFG {
    public static void main(String[] args)
    {
  
        // Create the list with null values
        List<String> list = new ArrayList<>(
            Arrays.asList("Geeks",
                          null,
                          "forGeeks",
                          null,
                          "A computer portal"));
  
        // Print the list
        System.out.println("Initial List: " + list);
  
        // Removing nulls using List.remove()
        // Repeatedly call remove() till all null are removed
        while (list.remove(null)) {
        }
  
        // Print the list
        System.out.println("Modified List: " + list);
    }
}
```


```java=
import com.google.common.base.Predicates;
import com.google.common.collect.Iterables;
import java.util.*;
  
class GFG {
    public static void main(String[] args)
    {
  
        // Create the list with null values
        List<String> list = new ArrayList<>(
            Arrays.asList("Geeks",
                          null,
                          "forGeeks",
                          null,
                          "A computer portal"));
  
        // Print the list
        System.out.println("Initial List: " + list);
  
        // Removing nulls using Guava Iterables
        // using Predicate condition isNull()
        Iterables.removeIf(list, Predicates.isNull());
  
        // Print the list
        System.out.println("Modified List: " + list);
    }
}
```