---
title: 使用 SVP 補幀 4K 螢幕小記
date: 2019-12-29 14:33:15
tags: [補幀, SVP]
categories: 影音
---

最近買了 ET322QK C 螢幕
有 4K 跟 10bit 畫質
順便也換了 Nivda GTX 2060 顯示卡
之前舊的顯示卡是用 AMD
有使用 AMD FM 補幀
最近設定補幀研究設定
在這邊做個簡單紀錄
(不一定適合每一台電腦)
當然我也遇到一些問題

<!--more-->

## 相關教學文章

* [Potplayer+Lav Filters+madVR - 知乎](https://zhuanlan.zhihu.com/p/33615747)
* [madVR - 知乎](https://zhuanlan.zhihu.com/p/52347040)
* [实现PC视频播放最强画质教程( Potplayer播放器+MADVR插件)](http://m.hangge.com/news/cache/detail_1461.html)
* [madvr 720p番剧播放非专业测试 - 第2页 - 电脑讨论 - Chiphell - 分享与交流用户体验](https://www.chiphell.com/thread-1922811-2-1.html)
* [Fw: [心得] SVP/FM +MADVR設定 - 看板 PC_Shopping - 批踢踢實業坊](https://www.ptt.cc/bbs/PC_Shopping/M.1554154069.A.4B6.html?fbclid=IwAR1dQUTWlp4hKBQ16QnCXP7zJiVujmTEsm2nSf7ZWCBAtHjo-Hj9T30Uhq4)

[[VCB-Studio 科普教程 2.2] 基于 PotPlayer 和 madVR 的播放器教程（已更新 XySubFilter） | VCB-Studio](https://vcb-s.com/archives/7228)
我有用這篇，推薦沒用 SVP 補真可以使用這個方式
通常在設定 MadVR 改成 10bit 跟 NGU Anti-Alias (scale)

## 補幀方法

一般電影我覺得不用做補幀動作
[(1) 【极客周刊】玩游戏为何要60帧才流畅，电影却只需24帧？ - YouTube](https://www.youtube.com/watch?v=--OKrYxOb6Y)
我覺得這邊說的很清楚了


### SVP 補幀

前幾天 SVP Pro在半價
又找不到便宜的二手顯示卡
所以就決定衝了

使用下也遇到很多問題
推薦使用 MPC-HC 播放器
SVP 跟 Potplayer 使用好像沒辦法 10 bit
[SVP 4 User Manual - SmoothVideo Project (SVP) - frame doubling interpolation](https://www.svp-team.com/wiki/Manual:SVP)
所以我後來使用 SVP Pro 裡面內建的 MPC-HC
在設定上懶的在調整

#### SVP、MPC 設定

svp
裡面安裝的都裝一裝
除了你不想用的 youtube 套件
{% asset_img 1577603064105.gif SVP設定_1 %}
{% asset_img Snipaste_2019-12-29_15-11-04.png SVP設定_2 %}
{% asset_img Snipaste_2019-12-29_15-08-44.png SVP設定_3 %}

mpc
我看到很多人設定都要載入`Lav Video/Audio`
mpc-hc不需要 load
原因 mpc 有分成兩種
反正 mpc-hc 有包`Lav Video/Audio`
不需做什麼額外設定
算滿方便的

我這邊設定
相關`Lav Video/Audio`可參考[[VCB-Studio 科普教程 2.2] 基于 PotPlayer 和 madVR 的播放器教程（已更新 XySubFilter） | VCB-Studio](https://vcb-s.com/archives/7228)
我不確定是不是 SVP 安裝好會用好設定
{% asset_img 1577612497847.gif _1_ %}
{% asset_img 1577612568993.jpg _2_ %}
這邊字幕一定要換**XySubFilter**
不然有時候某些片段會 Lag 掉幀


聲音可以用獨占模式，請參考這篇設定[黑米奇SVP: 基本教程系列（四）　－　播放軟體 MPC-HC（2017-06-02更新）](https://blackmickeysvp.blogspot.com/2017/04/mpc-hc.html)
「選項→播放器→輸出→音訊譜製器→內部音訊譜製器」
「內部音訊譜製器→獨占模式」另外允許「位元流」可以考慮打開(我不確定那個是做什麼用)

#### 缺點、改善方法

##### 內嵌字幕扭曲/偽影

老實說看動畫幾個非常動態畫面
內砍字幕會被影響到
但我也有查到改善方法[睇片研究所: SVP artifacts masking(偽影去除)設定對畫質的影響及教學](https://ppkkkp.blogspot.com/2016/07/svp-artifacts.html)
SVP 著色器 設定`複雜`就比較沒這個問題
大部份這個設定就能改善
但幾個動態場景還是能感受到
這邊用「未日時在做甚麼?有沒有空?可以來拯救嗎?」幾個片段

{% asset_img 1577609946521.gif _1_ %}
{% asset_img 1577610251475.gif _2_ %}

另一個方法就是設定補插幀模式`1.5m (少量偽影)`
但流暢度有降底，我覺得沒是有補到幀，只是沒上面的流暢

{% asset_img 1577610711014.gif _1_ %}
{% asset_img 1577610814227.gif _2_ %}

不能說到完全沒有，但真的比較少

至於哪個比較好，看個人喜好選擇

MadVR (Ctrl+J) 看到 `dropped frames` 沒有看到明顯掉幀

播放影片左下角會有 SVP 圖示
其實可以在`應用程式設定→顯示OSD資訊`可以關閉
但我覺得打開可以很明顯知道是不是有正常啟動SVP
還算滿方便的

還滿適合用外掛字幕來看

詳細設定可參考:[黑米奇SVP: SmoothVideo Project 系列（一）　－　安裝丶簡易設定（2019-05-04更新）](https://blackmickeysvp.blogspot.com/2017/06/smoothvideo-project.html)
### 雙顯卡

AMD + Nivda 做補幀，先講一下!!
由於我朋友是用這個方法，他一樣使用Nivda GTX 2060 和 ET322QK C 螢幕，而外買二手 2000多塊 RX 560
可以參考這篇這定[Fw: [心得] SVP/FM +MADVR設定 - 看板 PC_Shopping - 批踢踢實業坊](https://www.ptt.cc/bbs/PC_Shopping/M.1554154069.A.4B6.html?fbclid=IwAR1dQUTWlp4hKBQ16QnCXP7zJiVujmTEsm2nSf7ZWCBAtHjo-Hj9T30Uhq4)

使用看畫面流暢，不過 MadVR (Ctrl+J) 看到 `dropped frames` 有增加
不過自己看畫面流暢比較重要，畢竟自己感覺比較重要

FM 補幀畫面雖然沒有 SVP 比較流暢…，但相對偽影也相當少(應該是沒有)
但相對 兩個 GPU 使用率其中一個也佔滿多的
跟 SVP 遇到狀況也差不多

**但這個有很大的缺點，你的 Power 要夠力阿!!!!**

## 遇到問題

### 真 4K 影片卡頓

就暫時關掉 SVP 或 MadVR，我目前最大 1080P 居多
真人電影舊不需要開補幀

### 顯示卡嘯叫

在電腦預設裝 MadVR 設定高畫質顯卡會發出聲音
預設安裝不會有這種聲音
後來裝Heaven Benchmark  跑 3D 軟體也發現有這個聲音
只要一關掉影片(或測試顯卡軟體)聲音馬上就不見


聲音跟這個一樣:RTX 2070 嘯叫(https://www.bilibili.com/video/av39265016/?redirectFrom=h5)


我用 MSI Afterburner把風扇關掉 ，有肉眼確認風扇沒有再轉，聲音還是有...

網路上有看到幾個方法，
1. 更換顯示卡、主機板、電源供應器(但有些人換有失敗的，所以我不敢買)
2. 顯卡供電口加濾波電容  (但我看到好像沒有人在賣8 pin + 濾波電容)
顯示卡 電流聲 高頻音 coil whine 解決方法(http://joeartanis.blogspot.com/2015/12/coil-whine.html)
有看這篇說能解決問題
但我找不到哪裡有在賣orz
3. 膠水大法
https://www.bilibili.com/video/av8576153?from=search&seid=12894762763554126401
裡面測試失敗

聽說比較高階顯卡會遇到這個問題

**目前解決方法，把NGU設定調低**
尤其搭 SVP 的時候叫的更明顯
不是越高就越好

後來我不斷測試調設定
發現 MSI Afterburner 看片的時候
GPU clock 飆到 紅色三角形就會嘯叫
GPU clock 會隨著調整 NGU 飆高

{% asset_img forum1.png msi %}
{% asset_img forum2.png list %}

相關問題我有在[各位有沒有遇到顯示卡嘯叫(高頻音)情況? - 灌水聊天 - VCB-Studio分享論壇 - Powered by Discuz!](http://bbs.vcb-s.com/thread-5115-1-1.html)發問


## 結論

網路上有各式各樣設定，我覺得不用比哪個設定比較好，自己看得舒服真的比較重用
SVP 是非常順暢，但是偽影、字幕扭曲、快轉lag(聽說有比以前好)真的是很大缺點
FM 不是每一個影片都會做補真
但是 FM 快轉不會頓、偽影字幕扭曲這個優勢真的很大
雙顯示卡方法補真也是不錯選擇
但每一個方法各有優缺點
大家可以選擇對自己有利的方法
自己看的舒服比較重要
