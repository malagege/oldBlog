---
title: Java / Jboss 小記
date: 2020-03-10 20:36:37
tags: [java,tomcat,jboss]
categories: Java
---

最近工作需求，需要使用 Java
這邊做一些有關 Java 雜記

<!--more-->

常常我們配置 Eclipse 都沒有想過 Java 運作原理
藉著 GUI 操作我們也不知道它底下是怎麼跑的
雖然大學有玩過用 `java -cp xxxx` 來編譯一些程式
[go安裝筆記&gopath環境變數設定 | 程式狂想筆記](https://malagege.github.io/blog/2018/08/27/go%E5%AE%89%E8%A3%9D%E7%AD%86%E8%A8%98-gopath%E7%92%B0%E5%A2%83%E8%AE%8A%E6%95%B8%E8%A8%AD%E5%AE%9A/)
對 classpath 只能說編譯時候可以用別的 lib(或別的class) 搭配使用
不過我發現用 Eclipse 我不知道他是怎麼執行
今天主要好好研究

## Java

### Java Jar 檔案衝突

[【经验总结】Java利用iText实现PDF操作-技术-LittleblackOnline](http://littleblack.online/?thread-31.htm)

最近公司 jasperreport 和 itextpdf 衝突 
為了做 pdfwriter 做 setEncryption 

> PdfWriter.ENCRYPTION_AES_128
> 允許列印,編輯，複製，簽名 加密級別：128-bit-AES
> PdfWriter.STANDARD_ENCRYPTION_128
> 允許列印,編輯，複製，簽名 加密級別：128-bit-RC4

[【itext學習之路】-------（第三篇）對pdf文件進行加密和許可權設定 - IT閱讀](https://www.itread01.com/content/1541909906.html)
[Java 動態載入jar和class檔案例項解析 | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/298626/)
[Tomcat启动时classloader加载顺序 _Java_天乙贵人-CSDN博客](https://blog.csdn.net/myk_666888/article/details/5705208)
[ClassLoader解决jar包冲突问题_JavaScript_Peter_K的专栏-CSDN博客](https://blog.csdn.net/Peter_K/article/details/1667674)
[你必须知道的 17 个 Composer 最佳实践（已更新至 22 个） | PHP 技术论坛](https://learnku.com/php/t/7609/you-have-to-know-17-composer-best-practices-updated-to-22)

但會遇到
> java.lang.ClassNotFoundException: org.bouncycastle.asn1.DEREncodable

但用另一個 jar ，jasperreport 就會有衝突

簡單尋找衝突方式
看 Maven 對應版本(Compile Dependencies )
[ jasperreports » 6.1.0](https://mvnrepository.com/artifact/net.sf.jasperreports/jasperreports/6.1.0)→[ itext » 2.1.7](https://mvnrepository.com/artifact/com.lowagie/itext/2.1.7)→[bcprov-jdk15on 138](https://mvnrepository.com/artifact/bouncycastle/bcprov-jdk14/138)
[itextpdf » 5.5.5](https://mvnrepository.com/artifact/com.itextpdf/itextpdf/5.5.5)→[bcprov-jdk15on 1.49](https://mvnrepository.com/artifact/org.bouncycastle/bcprov-jdk15on/1.49)

依照 classpath 規則，正常 jar 檔相同 namespace 只會吃到第一個，之後讀到都會被忽略
正常解法應該要用對應版本，可以以框架為主，通常搭配的 jar 應該使用對應相同的版本會比較好

當然也有找到下面方法，但我覺得不是最好的做法
[classloader解决jar冲突隔离_Java_到西伯利亚浪-CSDN博客](https://blog.csdn.net/Memery_last/article/details/83830475)

## 使用 Maven 

之前也有使用 Maven 但也不是很了解
[Mave教學 | Maven 初學者中文教程](https://kentyeh.github.io/mavenStartup/index.html)
但我這邊發現所有的套件都會在`~/.m2`

也就是說，所有的 Project 都是吃一樣的 jar 檔案
這邊真的還滿奇特

不知道他 javac 是怎麼加檔案進去

[javac、classpath的编译和生成Maven工程 - 简书](https://www.jianshu.com/p/86694b6fd29c) {% asset_link web1.png 備份圖 %}

看上面這篇 classpath 可以放 Project 資料夾路徑沒編譯的 java? 有待確認...，不過應該沒錯

[Java包管理的那些事4——Maven - 知乎](https://zhuanlan.zhihu.com/p/65388744)
[開發中：給大家一份Maven知識整理，希望有所幫助 - 每日頭條](https://kknews.cc/zh-tw/code/nkqnleq.html)

因為目前公司碰的專案沒有用到 Maven和 Gradle
有機會接觸再補充

[專案自動構建工具對比(Maven、Gradle、Ant) - IT閱讀](https://www.itread01.com/content/1545095402.html)
[Maven 的基本概念與在 eclipse 專案實作 @ MISTECH 技術手抄本 :: 隨意窩 Xuite日誌](https://blog.xuite.net/mistech/blog/211079202-Maven+%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5%E8%88%87%E5%9C%A8+eclipse+%E5%B0%88%E6%A1%88%E5%AF%A6%E4%BD%9C)

## Eclipse 相關環境設定的影響

[昭佑.天翔: Eclipse Project Facet 功能](https://tomkuo139.blogspot.com/2012/01/eclipse-project-facet.html)
[Eclipse Build Path 的理解 - IT閱讀](https://www.itread01.com/content/1514264308.html)

## 部屬 java

[Jenkins入门(一)_运维_weixin_43112000的博客-CSDN博客](https://blog.csdn.net/weixin_43112000/article/details/84032214)
[Jenkins+Java+Maven+不中断服务+自动化部署+失败自动回滚_Java_weixin_43112000的博客-CSDN博客](https://blog.csdn.net/weixin_43112000/article/details/96858469)
[基于Jenkins的JAVA持续部署环境搭建 | 大专栏](https://www.dazhuanlan.com/2019/09/25/5d8a43a62f610/)

## Jboss

記得要設定 JBOSS_HOME
JBOSS_HOME = C:\jboss-4.0.3SP1

### jsp 熱加載

jboss-eap-7.1\standalone\configuration\standalone.xml  裡面 <jsp-config>改成<jsp-config development="true"/>  就能不用重啟jsp了


### jboss 使用 eclipse 沒有 log

移除 Server 我發現 log 消失了
後來突然想到有 JBoss EAP Server 點兩下，選擇 Open Launch configuration ，然後在 VM arguments 裡最後加入 -Dorg.jboss.as.logging.per-deployment=false 

參考:[JBoss EAP 6.x 對於 Log4j & System.out 在 Eclipse 上 Console 顯示設定 @ MISTECH 技術手抄本 :: 痞客邦 ::](https://mistech.pixnet.net/blog/post/393201464-jboss-eap-6.x-%E5%B0%8D%E6%96%BC-log4j-%26-system.out-%E5%9C%A8-eclipse-%E4%B8%8A-cons)

### java 熱加載

使用 debug 模式就能做到熱加載


### Eclipse 的 .classpath .project .settings

> 從數據上我們容易看出，上面描述了工程的依賴文件：
> 
> 源文件的具體位置（kind="src"）
> 運行的系統環境（kind="con"）
> 工程的library的具體位置信息(kind="lib")
> 在每個lib的xml子節點中，有關於它的其它配置信息（例如我配置的那個"javadoc_location"）
> 項目的輸出目錄(kind="output")
> 總體上說這個文件就是配置整個工程的運行環境。

[eclipse项目中的.settings .project .classpath 个人见解_开发工具_只在朝暮间的博客-CSDN博客](https://blog.csdn.net/qq_35792598/article/details/76573943)
[除了Maven，还有一种解决jar包管理及定制的好方法 - Java开发分享 - SegmentFault 思否](https://segmentfault.com/a/1190000007721071)

### Tomcat 一些事情

[理解Tomcat的Classpath-常见问题以及如何解决_Java_AndyElvis的专栏-CSDN博客](https://blog.csdn.net/andyelvis/article/details/6719996)



## struct2

這邊簡單小記，有空再補

action 

spa!xxx
spa 找 xml 對照的 java
xxx 是 method
沒寫 xxx 預設是 execute

頁面傳到 jsp 會做 setXXXX,getXXX


## struct2 和 spring

[Struts2 Spring集成_w3cschool](https://www.w3cschool.cn/struts_2/struts_spring.html)


## Eclipse 修改檔案不用重啟 Server

[eclipse配置修改代码不用重启_开发工具_小白菜-CSDN博客](https://blog.csdn.net/xingsfdz/article/details/80665896)
[Eclipse 修改方法裡面的內容不用重啟專案 - IT閱讀](https://www.itread01.com/content/1542335343.html)

[jboss + eclipse 如何設置才能不重啟服務就能自動編譯jsp - 开发者知识库](https://www.itdaan.com/tw/191c35d4309929e8a8154a0d6553bce2)
[Java碼農清單: jboss 修改jsp ,不須重啟servers伺服器](https://taiwanjava.blogspot.com/2016/04/jboss-jsp-servers.html)

https://tpu.thinkpower.com.tw/tpu/articleDetails/557


## Eclipse Project clean

以前開發經驗都知道 Project 執行有問題
只需要按 Project → clean
有找到他是清除 class 文件

> 所以有些時候編譯不通過原因是沒有project->clean造成的！！！
> eclipse > project > clean 是把 eclipse 編譯生成的 class 之類的文件刪除，以便以後再次編譯。 redeploy 是指把 web 重新部署到 tomcat， 這可能會把 jsp/html/css/js 之類的文件及 WEB-INF 目錄複製到 tomcat 或者是把整個項目導致成一個 war 複製到 tomcat。
[eclipse中project->clean的作用是什么 - THISISPAN - 博客园](https://www.cnblogs.com/panxuejun/p/6180324.html)

> 在eclipse中写JavaWeb项目时，有时候会出现代码修改了，但是执行的效果还是修改之前的，这时候clean一下就会解决问题
> 
> 1.clean操作
> 
> Project---->clean--->选择需要clean的项目
> 
> ![](https://i.imgur.com/81UjZvs.png)
> 
>  
> 
> 2.clean原理
> 
> eclipse为了提高效率，并不是每次启动项目都会检查插件，通过clean就是强制eclipse去检查已安装插件。
> 我们都知道.java文件是通过编译成.class文件运行的，而clean后会删除已经编译生成的.class文件并重新部署项目。
> 总起来将就是强制检查已安装插件，清除以前编译的信息，重新部署项目。
> 
> 3.clean后无法正常运行
> 
> ![](https://i.imgur.com/PFJYmNX.jpg)
>
> 右键项目---->properties如图，去掉Abort build when build path errors occur
[eclipse中的clean操作 - debugger.wiki - debugger.wiki](https://www.debugger.wiki/article/html/1562671380956639)


 發現還有 Deployment Assembly
 java Build path是編譯路徑設置,主要用來設置源代碼的編譯路徑默認是default output folder
> ![](https://i.imgur.com/o8Y3mbk.png)
> Web Deployment Assembly是eclipse中的發布路徑設置，DeployPath表示每個資源發佈之後的文件路徑
> ![](https://i.imgur.com/zEmk1Tf.png)
> 
> 理解
> 
> eclipse中使用tomcat使用啟動web項目的流程是 java build path編譯項目源代碼生成的class文件放到buildpath的設置路徑中，根據web deployment assembly將項目中的各個資源發佈到設置的指定文件中
[eclipse中Deployment Assembly选项设置说明_开发工具_我的世界，真实可见 -CSDN博客](https://blog.csdn.net/shiwodecuo/article/details/62040447)

這樣我就突然想到之前專案設錯 Deployment Assembly 就不能執行
不是 Web 專案是沒有 Deployment Assembly 

### eclipse 設定編譯出位置 .class path

應該是相同路徑
主要看.classpath
```
<classpathentry kind="src" path="src"/>
```

其他相關
[Path to .class file in eclipse - Stack Overflow](https://stackoverflow.com/questions/17161237/path-to-class-file-in-eclipse)
[在eclipse中使用Gradle之修改default output folder « Jason's Blog](http://jason79-blog.logdown.com/posts/256196-in-eclipse-using-gradle-to-modify-default-output-folder)

#### Eclipse 看 Properties 會亂碼

安裝 PropertiesEditor 就能正常
可參考下面連結
[Eclipse Properties 顯示中文字 | Xuan's Blog](https://coffee0127.github.io/blog/2016/08/15/eclipse-poperties/)
[eclipse中 XXXX.properties文件 不显示中文，显示的unicode编码。_开发工具_mukvintt的博客-CSDN博客](https://blog.csdn.net/mukvintt/article/details/79746857)


## vscode 使用 web 方案

### 先說明我目前用到的專案
.classpath 底下有設定這些
```xml
<?xml version="1.0" encoding="UTF-8"?>
<classpath>
	<classpathentry kind="src" path="src"/>
	<classpathentry kind="con" path="org.eclipse.jst.j2ee.internal.web.container"/>
	<classpathentry kind="con" path="org.eclipse.jst.j2ee.internal.module.container"/>
	<classpathentry combineaccessrules="false" kind="src" path="/xxxx-common"/>
	<classpathentry combineaccessrules="false" kind="src" path="/xxx-sms"/>
	<classpathentry kind="con" path="org.eclipse.jst.server.core.container/org.jboss.ide.eclipse.as.core.server.runtime.runtimeTarget/JBoss EAP 7.1 Runtime"/>
	<classpathentry combineaccessrules="false" kind="src" path="/xxxx-pdf"/>
	<classpathentry kind="con" path="org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.8">
		<attributes>
			<attribute name="owner.project.facets" value="java"/>
		</attributes>
	</classpathentry>
	<classpathentry kind="output" path="build/classes"/>
</classpath>
```

看到 add classpath 有設定一些專案旁的 Project
vscode 吃的到 .classpath 檔案
但 vscode 單讀開 Project 不會吃到的樣子
有找到下面的方法 java.project.referencedLibraries
但還是沒用

###  vscode 載入 jar 檔方法

```
"java.project.referencedLibraries": {
    "include": [
        "library/**/*.jar",
        "/home/username/lib/foo.jar"
    ],
    "exclude": [
        "library/sources/**"
    ],
    "sources": {
        "library/bar.jar": "library/sources/bar-src.jar"
    }
}
```

[Maven Support, Java Package, and Dependency Management in Visual Studio Code](https://code.visualstudio.com/docs/java/java-project)

In case VS Code throws an error for a classpath issue, try setting your classpath manually by either setting the CLASSPATH environment variable or editing the .classpath file with the path to the JAR file:

看樣子 maven 不能設定 classpath

相關連結
[Maven管理的jar没有发布到WEB-INF/lib下的解决方案_java的涟漪-CSDN博客](https://blog.csdn.net/rogerjava/article/details/23764679)
[（三）Java资源文件和路径相关扫盲 - 知乎](https://zhuanlan.zhihu.com/p/68065612)
[java项目中的classpath到底是什么 - 简书](https://www.jianshu.com/p/7cadd9bedf4f)
[java - Can I add jars to maven 2 build classpath without installing them? - Stack Overflow](https://stackoverflow.com/questions/364114/can-i-add-jars-to-maven-2-build-classpath-without-installing-them)

沒法對 Project 做
但這個對我的專案沒什麼用

### vsocde 對整個 workspace 開啟

```xml
<classpathentry combineaccessrules="false" kind="src" path="/xxxx-common"/>
```

發現對整個 workspace 開啟
程式就可以關連到 Project 程式
手動加真的太慢了
用 Eclipse 做設定會寫到 .classpath 檔案
缺點還是需要手動加

{% asset_img add_jar.png Eclipse加jar檔 %}

這個方法是參考[[vscode] java 사용시 세팅 관련](https://min9nim.github.io/2018/05/vscode-setting/)
原本我是想用 `*` 加入 jar 檔案
但這邊說不能這樣設定
使用單筆 jar 進去 vsocde就能順利認得 class


題外話
小記 Classpath 加 Project 時候
發步 JBOSS 時候 WEB-INF/lib/裡面會加入到剛剛加的 Project WAR 檔


### add project(classpath)

A Project 包 jar 檔時候，被 B Project 引入時候，我發現 A project 

[java的打包成jar方法 - 學而時習之](https://sites.google.com/site/waue0920/Home/java/java-de-da-bao-chengjar-fang-fa#%E6%89%93%E5%8C%85%E6%88%90exe%E6%AA%94%E5%B7%A5%E5%85%B7)




[intellij与eclipse默认快捷键对比 - 简书](https://www.jianshu.com/p/658ebd054fba)
[Maven查找依赖树，解决jar包冲突问题_Java_Epic丶玖遇-CSDN博客](https://blog.csdn.net/keshacookie/article/details/50506428)
[Jar 包依赖冲突排查思路和解决方法（logback + slf4j-log4j12） - 掘金](https://juejin.im/post/5ce90722f265da1bd522aa70)
[重新看待Jar包冲突问题及解决方案 - 簡書](https://www.jianshu.com/p/100439269148)


## ANT

[[JAVA] 用Ant打包war的簡單流程 | 阿輝的零碎筆記 - 點部落](https://dotblogs.com.tw/grayyin/2016/07/26/203656)