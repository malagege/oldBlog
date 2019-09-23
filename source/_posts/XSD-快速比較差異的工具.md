---
title: XSD 快速比較差異的工具
date: 2019-09-11 21:16:13
tags: [xsd]
categories: XML
---

最近收到要異動 XML
在很大的 XML 中
我需要尋找異動的 tag
尋找有用工具來分析 XSD

<!--more-->

## NVM 安裝

[Chocolatey Gallery | Maven 3.6.1.20190711](https://chocolatey.org/packages/maven)

快速解決安裝環境的問題XD

## Schema Diff Command Line Tool

[Schema Diff Tool](https://www.membrane-soa.org/soa-model-doc/1.4/cmd-tool/schemadiff-tool.htm)

可這邊下載檔案`soa-model-distribution-1.6.0-bin.zip`
解壓縮出來
需要設定 `SOA_MODEL_HOME` 環境變數
再設定 path += `%SOA_MODEL_HOME%\bin`

更懶的話，直接到`bin`資料夾做

```bash
schemadiff <<first schema>> <<second schema>> [<<output directory>>]
C:\soa-model-distribution-1.4.X>bin\schemadiff samples\diff\original\article.xsd samples\diff\modified\article.xsd schema-diff-output
```

會產生 HTML 檔案
這可說是非常完美的格式
有順利執行

## xsdiff

[valters/xsdiff: Compare XML XSD schemas with semantic diff, simple and straight differences report. Perfect if you need to see what has changed in huge schema file.](https://github.com/valters/xsdiff)

安裝很簡單

```bash
mvn clean install
# 單個檔案
java -jar XsDiff-app/target/xsdiff-app-1.0.0.jar old/xxx.xsd new/xxx.xsd
# 多個比較
# 記得每個資料夾需要放schema.lst XSD 清單
java -jar XsDiff-app/target/xsdiff-app-1.0.0.jar old/ new/
```

[xsdiff/Main.java at 0a31b666979a88e4bdf49b29c32947864a0a965c · valters/xsdiff](https://github.com/valters/xsdiff/blob/0a31b666979a88e4bdf49b29c32947864a0a965c/XsDiff-app/src/main/java/io/github/valters/xsdiff/app/Main.java#L61)

有順利跑出來，但是比較像文字 diff 比較


## XSD Compare

[yoep/xsd-compare: Compare different XSD version files to each other](https://github.com/yoep/xsd-compare)

因為我裝JAVA 10
所以需要改`pom.xml`

[javafx-maven-plugin/javafx-maven-plugin: Maven plugin for JavaFX](https://github.com/javafx-maven-plugin/javafx-maven-plugin)
[Support for current JDK 9 and JDK 10 · Issue #287 · javafx-maven-plugin/javafx-maven-plugin](https://github.com/javafx-maven-plugin/javafx-maven-plugin/issues/287)
```xml
<pluginRepositories>
    <pluginRepository>
        <id>oss-sonatype-snapshots</id>
        <url>https://oss.sonatype.org/content/groups/public/</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
    </pluginRepository>
</pluginRepositories>
```

上面 xml 放在 `<build>`前面

```xml
            <plugin>
                <groupId>com.zenjava</groupId>
                <artifactId>javafx-maven-plugin</artifactId>
                <version>8.9.0-SNAPSHOT</version>
                <configuration>
                    <appName>XSD Compare</appName>
                    <title>XSD Compare</title>
```
在修改版本

```bash
mvn clean install
# 完成編譯，可直接執行
java -jar ./target/xsd-compare-0.0.6-SNAPSHOT.jar
```

GUI 介面看起來很炫
但是我的 xml 不能開啟 orz




## 小記

目前還是`Schema Diff Command Line Tool`最實用
不果改天找到更好用的工具在更新
