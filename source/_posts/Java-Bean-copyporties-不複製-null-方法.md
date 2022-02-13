---
title: Java Bean copyporties 不複製 null 方法
date: 2022-02-14 00:00:28
tags: [java,bean,copyporties]
categories: Java
---

最近做 JS 做物件繼承值方法 (Object.assign) ，在想 Java 有沒有這個東西，但發現好像沒有，需要自己幹一個，我整理網路做好的。


<!--more-->

## 方法

[Skip null properties when copying properties in Spring BeanUtils](https://codippa.com/skip-null-properties-spring-beanutils/)

```java=
public static String[] getNullPropertyNames (Object source) {
    final BeanWrapper src = new BeanWrapperImpl(source);
    java.beans.PropertyDescriptor[] pds = src.getPropertyDescriptors();

    Set<String> emptyNames = new HashSet<String>();
    for(java.beans.PropertyDescriptor pd : pds) {
        Object srcValue = src.getPropertyValue(pd.getName());
        if (srcValue == null) emptyNames.add(pd.getName());
    }

    String[] result = new String[emptyNames.size()];
    return emptyNames.toArray(result);
}

// then use Spring BeanUtils to copy and ignore null using our function
public static void myCopyProperties(Object src, Object target) {
    BeanUtils.copyProperties(src, target, getNullPropertyNames(src));
}
```

### Java 8 實作方法

```java=
public static String[] getNullPropertyNames(Object source) {
    final BeanWrapper wrappedSource = new BeanWrapperImpl(source);
    return Stream.of(wrappedSource.getPropertyDescriptors())
            .map(FeatureDescriptor::getName)
            .filter(propertyName -> wrappedSource.getPropertyValue(propertyName) == null)
            .toArray(String[]::new);
}
```


- [java - How to ignore null values using springframework BeanUtils copyProperties? - Stack Overflow](https://stackoverflow.com/questions/19737626/how-to-ignore-null-values-using-springframework-beanutils-copyproperties)
- [Skip null properties when copying properties in Spring BeanUtils](https://codippa.com/skip-null-properties-spring-beanutils/)