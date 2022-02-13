---
title: '[轉]Java 底線命名轉駝峰'
date: 2022-02-13 23:43:20
tags: [java]
categories: Java
---

網路上找的方法，留一下紀錄。
<!--more-->

[java 下劃線轉駝峰 - IT閱讀](https://www.itread01.com/content/1549147695.html)
```java=
    /**
     * 將駝峰式命名的字串轉換為下劃線大寫方式。如果轉換前的駝峰式命名的字串為空，則返回空字串。</br>
     * 例如：HelloWorld->HELLO_WORLD
     * @param name 轉換前的駝峰式命名的字串
     * @return 轉換後下劃線大寫方式命名的字串
     */
    public static String underscoreName(String name) {
        StringBuilder result = new StringBuilder();
        if (name != null && name.length() > 0) {
            // 將第一個字元處理成大寫
            result.append(name.substring(0, 1).toUpperCase());
            // 迴圈處理其餘字元
            for (int i = 1; i < name.length(); i++) {
                String s = name.substring(i, i + 1);
                // 在大寫字母前新增下劃線
                if (s.equals(s.toUpperCase()) && !Character.isDigit(s.charAt(0))) {
                    result.append("_");
                }
                // 其他字元直接轉成大寫
                result.append(s.toUpperCase());
            }
        }
        return result.toString();
    }

    /**
     * 將下劃線大寫方式命名的字串轉換為駝峰式。如果轉換前的下劃線大寫方式命名的字串為空，則返回空字串。</br>
     * 例如：HELLO_WORLD->HelloWorld
     * @param name 轉換前的下劃線大寫方式命名的字串
     * @return 轉換後的駝峰式命名的字串
     */
    public static String camelName(String name) {
        StringBuilder result = new StringBuilder();
        // 快速檢查
        if (name == null || name.isEmpty()) {
            // 沒必要轉換
            return "";
        } else if (!name.contains("_")) {
            // 不含下劃線，僅將首字母小寫
            return name.substring(0, 1).toLowerCase() + name.substring(1);
        }
        // 用下劃線將原始字串分割
        String camels[] = name.split("_");
        for (String camel :  camels) {
            // 跳過原始字串中開頭、結尾的下換線或雙重下劃線
            if (camel.isEmpty()) {
                continue;
            }
            // 處理真正的駝峰片段
            if (result.length() == 0) {
                // 第一個駝峰片段，全部字母都小寫
                result.append(camel.toLowerCase());
            } else {
                // 其他的駝峰片段，首字母大寫
                result.append(camel.substring(0, 1).toUpperCase());
                result.append(camel.substring(1).toLowerCase());
            }
        }
        return result.toString();
    }
```