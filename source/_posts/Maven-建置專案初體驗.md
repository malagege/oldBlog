---
title: Maven 建置專案初體驗
date: 2020-06-15 20:09:20
tags: [mvn, java]
categories: Java
---

最近工作機會難得，有空摸 maven
沒想到想說簡單的建置小專案
就遇到一堆雷
在這邊筆記

<!--more-->

## 安裝

我用 chocolate 安裝

安裝可參考:
[How to Install Apache Maven on Ubuntu 18.04 | Linuxize](https://linuxize.com/post/how-to-install-apache-maven-on-ubuntu-18-04/)

## 請不要使用 Powershell!!

**請不要使用 Powershell!!**
**請不要使用 Powershell!!**
**請不要使用 Powershell!!**

很重要，所以我說三遍

就算用爛爛的 cmd ，也不要浪費大量時間處理 powershell 

好像是 Powershell -D 會有問題
我就沒有詳細研究了

[【坑】【maven】在PowerShell窗口下执行maven命令行报错：Unknown lifecycle phase .test.skip=true._三年小白的博客-CSDN博客](https://blog.csdn.net/wushengjun753/article/details/78973618?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase)

用 Powershell Script 大概會看到

```
[ERROR] Unknown lifecycle phase "/home/tucson/Projects/kafkaGuideTest/target/Getting-Started-0.0.1-SNAPSHOT.jar". You must specify a valid lifecycle phase or a goal in the format <plugin-prefix>:<goal> or <plugin-group-id>:<plugin-artifact-id>[:<plugin-version>]:<goal>. Available lifecycle phases are: validate, initialize, generate-sources, process-sources, generate-resources, process-resources, compile, process-classes, generate-test-sources, process-test-sources, generate-test-resources, p rocess-test-resources, test-compile, process-test-classes, test, prepare-package, package, pre-integration-test, integration-test, post-integration-test, verify, install, deploy, pre-site, site, post-site, site-deploy, pre-clean, clean, post-clean. -> [Help 1]
```

但絕非你的指令錯誤

## 建置 Maven

### 建置新專案

mvn archetype:create

Choose archetype: 	選擇建立Project的範本，預設是99:maven-archetype-quickstart建立一個最基本的Project
Choose version: 	選擇範本的版本，會列出一些範本可用的版本，其差異是就不用版本的範本可能會建立有不同的資源檔(比如可能附帶圖檔)
定義groupId: 	輸入要建立Project所隸屬的組織或公司，如我自已用idv.kentyeh.software
定義artifactId: 	就是Project名稱，例如 firstMaven
定義version: 	Project的版本號，預設是1.0-SNAPSHOT
定義package: 	初始建立的Java Package, 如 idv.kentyeh.software


#### Archetype有哪些

> Archetype ArtifactIds	說明
> maven-archetype-archetype	產生Archetype專案
> maven-archetype-j2ee-simple	產生簡單的J2EE專案
> maven-archetype-plugin	產生Maven plugin專案
> maven-archetype-plugin-site	產生Maven plugin site專案
> maven-archetype-portlet	產生JSR-268 Portlet專案
> maven-archetype-profiles	
> maven-archetype-quickstart	產生簡單的Maven專案
> maven-archetype-site	產生Maven site專案
> maven-archetype-site-simple	產生簡單的Maven site專案
> maven-archetype-webapp	產生Maven Web App專案
> 
> 
> 以上最常使用maven-archetype-quickstart建立簡單的Maven專案，
> 及使用maven-archetype-webapp建立Maven Web專案，剩下的很少用（我個人沒用過）。

[菜鳥工程師 肉豬: Maven 什麼是Archetype](https://matthung0807.blogspot.com/2019/07/maven-archetype.html)

[maven中的groupId和artifactId到底指的是什么？_孙雪峰-CSDN博客](https://blog.csdn.net/snowin1994/article/details/53024871)


### 一種快速輸入建置方法

maven-archetype-quickstart (Java Project)

```bash
mvn archetype:generate -DgroupId=com.yiibai.core -DartifactId=ProjectName -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```
maven-archetype-webapp (Java Web Project)

```bash
mvn archetype:generate -DgroupId=com.yiibai.web -DartifactId=ProjectName -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```
作者：park
链接：https://juejin.im/post/5a1e1f76f265da432b4a92cb
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。


題外話:
[Mave教學 | Maven 初學者中文教程](https://kentyeh.github.io/mavenStartup/index.html)
mvn archetype:create 我沒辦法使用...


## 加入依賴套件

```xml
 <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/commons-httpclient/commons-httpclient -->
    <dependency>
        <groupId>commons-httpclient</groupId>
        <artifactId>commons-httpclient</artifactId>
        <version>3.1</version>
    </dependency>
  <!-- https://mvnrepository.com/artifact/com.google.code.gson/gson -->
  <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.2.4</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/net.sf.json-lib/json-lib -->
  <dependency>
      <groupId>net.sf.json-lib</groupId>
      <artifactId>json-lib</artifactId>
      <version>0.9</version>
  </dependency>
  </dependencies>
  ```

## 編譯 Java

mvn compile

```
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------------------< sample:sample >----------------------------
[INFO] Building sample 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-resources-plugin:3.0.2:resources (default-resources) @ sample ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/xxx/sample/src/main/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.0:compile (default-compile) @ sample ---
[INFO] Nothing to compile - all classes are up to date
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.488 s
[INFO] Finished at: 2020-06-15T20:30:00+08:00
[INFO] ------------------------------------------------------------------------
```

## 執行 Java

mvn exec:java -Dexec.mainClass="sample.App"

```
[INFO] Scanning for projects...
[INFO] 
[INFO] ---------------------------< sample:sample >----------------------------
[INFO] Building sample 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- exec-maven-plugin:3.0.0:java (default-cli) @ sample ---
Hello World!
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.573 s
[INFO] Finished at: 2020-06-15T20:31:19+08:00
[INFO] ------------------------------------------------------------------------
```

## 使用 mvn 

在 win 我寫簡單 bat

```bat
call mvn compile
call mvn exec:java -Dexec.mainClass="sample.ExampleGetToken"
pause
```

這邊我踩到一個地雷
mvn 前面需要加 call

> 但是總是在執行的時候執行完一個mvn 目標後自動退出，pause命令也無效，分析原因為mvn本身是一個bat命令，因此在exit退出的時候，整個指令碼程序將退出，加入call命令呼叫即可。

參考來源:[呼叫maven命令的BAT指令碼分享 | 程式前沿](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/388797/)

### 科普小知識

Linux 沒有 pause

```bash
# method 1
read -n 1 -p "Press any key to continue..."

# method 2 
read -n 1 -p "Press any key to continue..." INP
if [ $INP != '' ] ; then
        echo -ne '\b \n'
fi
```

```bash
#! /bin/bash
function pause(){
        read -n 1 -p "$*" INP
        if [ $INP != '' ] ; then
                echo -ne '\b \n'
        fi
}
 
#使用時：
pause 'Press any key to continue...'
```

好啦，其實用 Linux 應該都是打指令呼叫程式
應該比較不會有 window 問題

其他資源:
- [bat結合maven 實現windows下maven自動打包指令碼 - IT閱讀](https://www.itread01.com/content/1544963239.html)
- [【坑】【maven】在PowerShell窗口下执行maven命令行报错：Unknown lifecycle phase .test.skip=true._三年小白的博客-CSDN博客](https://blog.csdn.net/wushengjun753/article/details/78973618?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase)
- [Maven创建项目时出现Generating project in Interactive mode就一直卡住的解决方案_程高伟-CSDN博客](https://blog.csdn.net/frankcheng5143/article/details/51813479)
- [菜鳥工程師 肉豬: Maven 什麼是Archetype](https://matthung0807.blogspot.com/2019/07/maven-archetype.html)
- [使用Maven模板创建项目 - 掘金](https://juejin.im/post/5a1e1f76f265da432b4a92cb)
- [整合 Maven 與 Yeoman，學習筆記 (2) - 使用 mvn archetype:generate 建立 JAX-RS / Jersey 專案 | 格物致知](https://amobiz.github.io/2013/12/27/integrating-maven-and-yeoman-study-notes-2-mvn-archetype-generate-jersey/)
- [How to Install Apache Maven on Ubuntu 18.04 | Linuxize](https://linuxize.com/post/how-to-install-apache-maven-on-ubuntu-18-04/)