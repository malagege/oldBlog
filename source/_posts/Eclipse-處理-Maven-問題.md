---
title: Eclipse 處理 Maven 問題
date: 2021-06-02 20:26:24
tags: [eclipse, maven]
categories: Java
---

各種奇怪整裡

<!--more-->


## Failure to transfer org.apache.maven.plugins:maven-compiler-plugin:jar:2.5.1

```
[INFO] Scanning for projects...
[WARNING] 
[WARNING] Some problems were encountered while building the effective model for wsclient:wsclient:jar:0.0.1-SNAPSHOT
[WARNING] 'build.plugins.plugin.version' for org.apache.maven.plugins:maven-resources-plugin is missing. @ line 60, column 21
[WARNING] 'build.plugins.plugin.version' for org.apache.maven.plugins:maven-compiler-plugin is missing. @ line 52, column 21
[WARNING] 
[WARNING] It is highly recommended to fix these problems because they threaten the stability of your build.
[WARNING] 
[WARNING] For this reason, future Maven versions might no longer support building such malformed projects.
[WARNING] 
[INFO] 
[INFO] Using the builder org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder with a thread count of 1
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building finvcbusclient Maven Webapp 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] Downloading: http://repo.maven.apache.org/maven2/org/apache/maven/plugins/maven-resources-plugin/2.6/maven-resources-plugin-2.6.pom
[INFO] ------------------------------------------------------------------------
```

發現 `http://repo.maven.apache.org/maven2/org/apache/maven/plugins/maven-resources-plugin/2.6/maven-resources-plugin-2.6.pom` 不能下載
加https 就可以了...

後來網路嘗試了各種方法(以下我沒成功，可能是不同狀況)
* [Failure to transfer org.apache.maven.plugins:maven-compiler-plugin:jar:2.5.1_weixin_30580341的博客-CSDN博客](https://blog.csdn.net/weixin_30580341/article/details/99592631)
* [eclipse加载maven工程提示pom.xml无法解析org.apache.maven.plugins:maven-resources-plugin:2.4.3解决方案_imlmy的专栏-CSDN博客](https://blog.csdn.net/imlmy/article/details/8268293)
* [eclipse建立maven錯誤訊息Could not calculate build plan: Plugin org.apache.maven.plugins:maven-war-plugin:2. | 阿輝的零碎筆記 - 點部落](https://dotblogs.com.tw/grayyin/2019/09/17/192207)

都沒有成功....

後來發現是 

```xml=
 <url>http://people.apache.org/repo/m2-incubating-repository/</url> 
```

不能訪問

```
  <url>https://repo.maven.apache.org/maven2/</url> 
```

修正完還是不行...


最後加了這個就正常了
[java - How to solve maven 2.6 resource plugin dependency? - Stack Overflow](https://stackoverflow.com/questions/31316339/how-to-solve-maven-2-6-resource-plugin-dependency/59872184#59872184)

```xml pom.xml
<repositories>
        <repository>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <id>central</id>
            <name>Central Repository</name>
            <url>https://repo.maven.apache.org/maven2</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <releases>
                <updatePolicy>never</updatePolicy>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <id>central</id>
            <name>Central Repository</name>
            <url>https://repo.maven.apache.org/maven2</url>
        </pluginRepository>
    </pluginRepositories>
```