---
title: 使用 AIX ibm-heapanalyzer 查詢 Java OOM 問題
date: 2021-10-09 23:26:11
tags:
categories: Java
---

最近接的案子城市遇到OOM，但有時候要判斷程式是哪邊造成的，需要一些工具。但詳細還是要看log，說不定發生大量查詢資料地方。

<!--more-->

[java.lang.OutOfMemoryError Java heap space? 怎麼解? - 技術雜記 Technology Notes - Jack Yu | 傑克](https://yu-jack.github.io/2020/02/24/java-oom/)![備份圖](https://i.imgur.com/VeD71Y2.png)


AIX 不會產生 phrof 工具，產生 phd 檔案。跑在分析工具不能跑。最後找了很久發現要用 IBM HeapAnalyzer。

[Java 執行緒究竟佔用多少記憶體_ImportNew - 微文庫](https://www.gushiciku.cn/dc_tw/201074884)

[Capturing a Java Thread Dump | Baeldung](https://www.baeldung.com/java-thread-dump)

[保姆級教學，22張圖揭開ThreadLocal_程式猿阿星 - MdEditor](https://www.gushiciku.cn/pl/gfP6/zh-tw)

.phd 檔案

IBM HeapAnalyzer

[IBM HeapAnalyzer](https://www.ibm.com/support/pages/ibm-heapanalyzer)

![](https://i.imgur.com/PVd7rhB.png)


![](https://i.imgur.com/v0czPEq.png)


![](https://i.imgur.com/FtJwJSZ.png)


![](https://i.imgur.com/11AOiZP.png)


可惜不知道哪個 Class發生錯誤，但可以找對應資料和log錯誤地方找是哪邊出問題。原本這支程式MQ進來會開對應Class執行緒去執行，因為同時間跑的程式有兩隻，最後有一隻會撈大量資料，導致JVM RAM 暴掉。最後把這個排程先移出去。