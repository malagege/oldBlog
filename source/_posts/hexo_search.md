---
title: "hexo裝上搜尋功能(local search)"
date: 2018-08-05 16:42:18
tags: [hexo,plugin]
categories: hexo
---

最近文章越來越多了
應該是時候加入收尋功能
有時找新文章真的太麻煩了
雖然很簡單還是筆記一下

<!--more-->

其實很簡單
簡單紀錄一下

1. 在hexo站點目錄下執行`npm install hexo-generator-searchdb --save`
2. Next主題config.yml設置
```
# Local search
local_search:
  enable: true
```
基本上這樣就可以了


##遇到奇怪錯誤問題
怎麼點搜尋一直轉圈圈

{% asset_img search.png hexo_search不能搜尋 %}

XML解析錯誤
```
  <entry>
    <title><![CDATA[Android手機電源鍵壞掉，開機方法]]></title>
    <url>%2Fblog%2F2018%2F05%2F19%2FAndroid%E6%89%8B%E6%A9%9F%E9%9B%BB%E6%BA%90%E9%8D%B5%E5%A3%9E%E6%8E%89%EF%BC%8C%E9%96%8B%E6%A9%9F%E6%96%B9%E6%B3%95%2F</url>
    <content type="text"><![CDATA[最近紅米NOTE3電源鍵壞了很久，但是平常可以指紋辨識解鎖有幾次不小心關機，但是強按還是有辦法開機但今天不行了….，剛好買新機想備份LINE，但是無法開機我Google尋了很多方法最終採取fastboot開機法 小米手机电源键坏了怎么办？怎么开机啊？安卓手机手机学院_脚本之家這一篇使用結果沒用 FastBoot 紅米NOTE3 進入方法 音量键- + 电源键 3秒但是…，電源鍵壞掉…無法使用不過可以插電源線充電插入時，按下音量鍵-可以進入fastboot模式 最後，[Linux] fastboot 用法學習歸納 - 代碼家1234sudo add-apt-repository ppa:nilarimogard/webupd8### 我沒有執行上面，發現內鍵可以安裝sudo apt-get updatesudo apt-get install android-tools-adb android-tools-fastboot 安裝FastBoot進入root，fastboot reboot你的手機就會重新開機 愉悅~~~ 手機設定畫面，第二章圖可以設定自動撥放 https://developers.google.com/web/updates/2017/09/autoplay-policy-changeshttps://times.hinet.net/news/21737359 [Linux] fastboot 用法學習歸納 - 代碼家adb &amp; fastboot 常用指令 – HTC Flash Zone]]></content>
      <categories>
        <category>Android</category>
      </categories>
      <tags>
        <tag>Android</tag>
      </tags>
  </entry>
```
我猜是我的文章有跟xml衝突到

最後github issue看到[关于文章里有非 utf-8 格式的字符时，搜索会卡住 · Issue #30 · wzpan/hexo-generator-search](https://github.com/wzpan/hexo-generator-search/issues/30)
改成JSON格式就能解決了

在hexo專案裡面config.yml(非主題裡面)
```
# search
## [第三方服务集成 - NexT 使用文档](http://theme-next.iissnan.com/third-party-services.html#local-search)
search:
  path: search.json
  field: post
  format: html
  limit: 10000
```

最後無聊逛到[| 牧之](http://muyunyun.cn/about/)
網站裡面有一隻貓
是用**hexo-helper-live2d**
[hexo live2d插件 2.0 ! | 幻想帖](https://huaji8.top/post/live2d-plugin-2.0/)
[EYHN/hexo-helper-live2d: Add the Sseexxyyy live2d to your hexo!](https://github.com/EYHN/hexo-helper-live2d)
先記錄一下


參考來源:
[Hexo博客添加搜索功能 | IT范儿](http://www.itfanr.cc/2017/10/27/add-search-function-to-hexo-blog/)
[第三方服务集成 - NexT 使用文档](http://theme-next.iissnan.com/third-party-services.html#local-search)