---
title: Word (docx) 轉 pdf 方法
date: 2021-06-27 14:43:05
tags: [java, php, word]
categories: 實用工具
---

之前有做過類似做處理 Word 文章
[php-excel-templator 用範本(template)快速產生Excel方法 | 程式狂想筆記](https://malagege.github.io/blog/2019/09/03/php-excel-templator-%E7%94%A8%E7%AF%84%E6%9C%AC-template-%E5%BF%AB%E9%80%9F%E7%94%A2%E7%94%9FExcel%E6%96%B9%E6%B3%95/)
[Word,Excel的相關template套件小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/05/05/Word-Excel%E7%9A%84%E7%9B%B8%E9%97%9Ctemplate%E5%A5%97%E4%BB%B6%E5%B0%8F%E8%A8%98/)
但沒對做轉 PDF 方法

<!--more-->

## .Net 方案
[Create Word (DOCX) or PDF on Docker container from .NET Core](https://www.gemboxsoftware.com/document/examples/create-word-pdf-on-docker-net-core/5902)


[Install Microsoft Office In Windows Container | by Jung-Hyun Nam | Dev Genius](https://blog.devgenius.io/install-microsoft-office-in-windows-container-ce05877138fd)

Microsoft.Office.Interop.Word


我後來想找 Docker 可以用的，但 MS Office 只能用在 Widnow ，但感覺用 MS Office 很難做 Docker 執行，後來發現找 OpenOffice 才找到有直接能用的，gotenberg 真的太棒了。

## 其他方案

[java如何實現word轉PDF？ - 知乎](https://www.zhihu.com/question/54953944)


> 目前就幾種方法
> 1、poi + itext。先轉html再繪製，聽說格式有差異。比較複雜，格式兼容差，跨平台。
> 2、借助openoffice我覺得不要選這個， openoffice 和 ms office 的格式差異挺大的，還不如jacob。
> 3、jacob + ms office需要借助本地 office ，格式兼容最好，最後的備選方案。
> 4、docx4j的export pdf組件。我覺得如果是格式不是很複雜的情況，應該用這個比較好（在office開源界，poi第一它> 應該第二），或者是第5項中的其他方案。plutext/docx4j-export-FO
> 5、aspose等其他組件。

參考一些方案找現成的

### convert-document

[occrp-attic/convert-document: A docker container for LibreOffice and unoconv, used to generate PDF files from office-type documents.](https://github.com/occrp-attic/convert-document)

可以使用，但是中文會變亂碼。但有猜是字型的問題，可參考: [解決OpenOffice格式轉換中文亂碼終極解決方案 - IT閱讀](https://www.itread01.com/content/1548558560.html)

```shell=
docker pull alephdata/convert-document
docker run -p 3000:3000 -ti alephdata/convert-document
```

### officeconverter

[EugenMayer/officeconverter: Convert file formats like docx, xlx to other formats like pdf, png - based on jodconverter and libreoffice](https://github.com/EugenMayer/officeconverter)

我執行不能使用...
但我有找到相關的
[EugenMayer/docker-image-jodconverter: Docker image with jodconverter + libreoffice for document conversion through a REST api](https://github.com/EugenMayer/docker-image-jodconverter)

```shell=
docker run --memory 512m --rm -p 8080:8080 eugenmayer/jodconverter:gui
docker run --memory 512m --rm -p 8080:8080 eugenmayer/jodconverter:rest
```

不知是我的電腦太爛??我執行公司複雜 Word 花了一分多鐘...


### office2pdf

[microacup/office2pdf: convert office document（word、ppt）to pdf](https://github.com/microacup/office2pdf)

ms office , openoffice Java 範例程式使用。
都有現成的程式，手動測試一下，發現 gradle 不能用(不知道是版本問題??)
`試一下改成將compile改成implementation，testCompile改成testImplementation,然後Sync`
就能正常行

build.gradle
```groovy=
group 'me.meiqiu.test'
version '1.0-SNAPSHOT'

apply plugin: 'java'

sourceCompatibility = 1.8

repositories {
    mavenLocal()
    mavenCentral()
}

dependencies {
    // OpenOffice依赖的
    implementation (
            'org.openoffice:unoil:4.1.2',
            'org.openoffice:juh:4.1.2',
            'org.openoffice:ridl:4.1.2',
            'org.openoffice:jurt:4.1.2',
            'org.jodconverter:jodconverter-core:4.2.0',
            'org.jodconverter:jodconverter-local:4.2.0'
    )

    // 调用windows office组件的适配器：jaccb.jar等
    implementation fileTree(dir:'libs',include:['*.jar'])

    testImplementation group: 'junit', name: 'junit', version: '4.12'
    testImplementation group: 'org.slf4j', name: 'slf4j-log4j12', version: '1.7.25'
}

```

記得要做
> JACOB is a JAVA-COM Bridge that allows you to call COM Automation components from Java. It uses JNI to make native calls to the COM libraries. JACOB runs on x86 and x64 environments supporting 32 bit and 64 bit JVMs . 需要把jacob-x.xx-x64.dll放到java/bin(与java.exe相同)目录下

![](https://i.imgur.com/10BXXO2.jpg)


#### SpringBoot使用LibreOffice轉換PDF 程式範例

[SpringBoot使用LibreOffice轉換PDF - SegmentFault 思否](https://segmentfault.com/a/1190000015129654)


#### gotenberg

[thecodingmachine/gotenberg: A Docker-powered stateless API for converting HTML, Markdown and Office documents to PDF](https://github.com/thecodingmachine/gotenberg)

gotenberg 這個很棒！中文不會亂碼。用 API 就能轉。原本一分鐘可以執行的，這個不到幾秒就出來了。

別的程式也能串接
- [kaipaysen/gotenberg.client.java: Java Client for Gotenberg](https://github.com/kaipaysen/gotenberg.client.java)
- [thecodingmachine/gotenberg-php-client: PHP client for the Gotenberg API](https://github.com/thecodingmachine/gotenberg-php-client)


## 彩蛋

發現作者還有滿有趣專案[thecodingmachine/workadventure: A collaborative web application (virtual office) presented as a 16-bit RPG video game](https://github.com/thecodingmachine/workadventure)


未來有空找個 Word 模板
[Generate documents from a Word template with Docx4j on Wildfly](https://rieckpil.de/howto-generate-documents-from-word-templates-with-docx4j-on-wildfly-14/)
[blog-tutorials/generate-documents-from-word-templates-with-docx4j-on-wildfly14 at master · rieckpil/blog-tutorials](https://github.com/rieckpil/blog-tutorials/tree/master/generate-documents-from-word-templates-with-docx4j-on-wildfly14)