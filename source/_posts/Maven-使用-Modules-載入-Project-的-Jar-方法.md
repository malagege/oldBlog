---
title: Maven 使用 Modules 載入 Project 的 Jar 方法
date: 2022-07-31 01:10:41
tags: [maven,java,jar]
categories: Java
---


## 前言

之前我們使用專案，需要透過 Eclipse 設定相關套件或Project 依賴。這邊我們看到 Maven 可以加載 Jar，對應相關 Project 卻沒有明確知道，最近發現 Maven 也能做到相同的事情。

![](https://i.imgur.com/Atng1B7.png)

我找到是使用 Module 方法，~~發現這水很深~~，，這邊也採了一些坑，這邊簡單做個紀錄。不會對全部功能做個假解。

<!--more-->

## 有關 Maven 文章整理

網路找到一些不錯文章，先整理在這邊。

- [Eclipse中Maven Module和Maven Project的区别_程序媛搬砖的博客-CSDN博客_maven module和maven project](https://blog.csdn.net/Love666Code/article/details/78859408) [備份圖](https://user-images.githubusercontent.com/6058558/168426302-901faf8e-6097-4281-b590-fd224e29ee17.png)
- [學 Maven，看松哥這一篇就夠了](https://mp.weixin.qq.com/s/9aMRFEXbh0-usVE96uZ8Ag) [備份圖](https://user-images.githubusercontent.com/6058558/168426263-0ef91223-486a-4529-b57c-0913a69837b6.png)
- [maven模組化管理 | 程式前沿](https://codertw.com/%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80/535965/)
- [Maven pom.xml中的元素modules、parent、properties以及import - 青石路 - 博客园](https://www.cnblogs.com/youzhibing/p/5427130.html) [備份圖](https://user-images.githubusercontent.com/6058558/168426381-081a8ddc-e2ad-4c17-9c5b-ac920b07a16a.png)
- [项目构建工具Maven-11多模块-继承-聚合-依赖_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Th411Z76q)


## 了解前最好了解東西

完全不熟的人可以推薦看一下[项目构建工具Maven-11多模块-继承-聚合-依赖_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Th411Z76q)影片，看完應該就大概了解怎麼使用。

### Properties

主要放置設定參數或使用者參數。

![](https://i.imgur.com/vuJ2wAv.png)

主要看`${poi.version}`。

```xml=
		<dependency>
			<groupId>org.apache.poi</groupId>
			<artifactId>poi</artifactId>
			<version>${poi.version}</version>
		</dependency>
```


### 父子層 dependencies、dependencyManagement

這個是重點，以為跟我踩到的雷有點關係。

這邊會分父層(上層) pom.xml 和 子層(下層) pom.xml。
寫在父層pom.xml一定可以繼承到子層 pom.xml。

寫在`dependencyManagement`的套件可以選擇性讓子層pom.xml做依賴動作，但版本可以訂在父層 pom.xml，子層可以省略或強制改寫，但我建議不寫比較好管理。

這邊 maven 在父層做包檔動作，會把所有子層做動作。但是載子層目錄下做只會做子層動作，所以`<parent>`設定不能做錯。


## 網路範例

[maven-muilti-module-demo/mvn-parent at master · Quison/maven-muilti-module-demo](https://github.com/Quison/maven-muilti-module-demo/tree/master/mvn-parent)

從上面我可以能知道 `<module>`的名稱可以放在依賴 dependencies tag。但不幸我都失敗了

### 我踩到什麼雷?

![](https://i.imgur.com/9OONdT6.png)


最後發現是要加 Version，等等父層加也沒用。後來發現子層一定要加上去。但比較推薦用`<version>${project.version}</version>`，通常依賴版本應該都會跟 maven其他依賴版本一致ˋ，可以這樣設定。很多文章這邊都沒講清楚，雖然我也不知道為什麼，但找到原因就這邊紀錄一下。


這邊跟上面依賴規則跟我說的不太一樣，所以剛開始設定就會踩到這個雷，原理也不知道是什麼。