---
title: Java 字串濾掉HTML
date: 2022-02-13 23:42:14
tags: [java]
categories: Java
---

網路上找的 Trim Html 程式方法。

<!--more-->

```java=
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class HTMLSpirit{
    public static String delHTMLTag(String htmlStr){
        String regEx_script="<script[^>]*?>[\\s\\S]*?<\\/script>"; //定義script的正則表示式
        String regEx_style="<style[^>]*?>[\\s\\S]*?<\\/style>"; //定義style的正則表示式
        String regEx_html="<[^>]+>"; //定義HTML標籤的正則表示式
        String regEx_script="[<＜]script[^>＞]*?>[\\s\\S]*?[<＜]\\/script[>＞]"; //定義script的正則表示式
        String regEx_style="[<＜]style[^>＞]*?>[\\s\\S]*?[<＜]\\/style[>＞]"; //定義style的正則表示式
        String regEx_html="[<＜][^>＞]+[>＞]"; //定義HTML標籤的正則表示式
        String regEx_space="\\s"; // 去除空白
        
        Pattern p_script=Pattern.compile(regEx_script,Pattern.CASE_INSENSITIVE);
        Matcher m_script=p_script.matcher(htmlStr);
        htmlStr=m_script.replaceAll(""); //過濾script標籤
        
        Pattern p_style=Pattern.compile(regEx_style,Pattern.CASE_INSENSITIVE);
        Matcher m_style=p_style.matcher(htmlStr);

```