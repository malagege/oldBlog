---
title: vLan 一些知識小記
date: 2022-02-13 22:59:33
tags: [vlan,switch,router]
categories: 網路
---

最近我想做~~無聊~~ Switch-lan-play 實驗，想建立VM做 vlan網路切割，最後還是失敗了。我參考[Linux下vlan的学习（下） | 污力大熊的博客](http://zhongm.in/2017/01/17/Learning-Linux-Vlan-2/)，這篇實作，其實我文本想說照著上面做，我應該可以不用了解太多就能做出環境，但最後失敗了，我有想到一些問題，因為我一直沒有想到我虛擬的另外一端要接什麼東西，就是下面那朵雲。

![](https://i.imgur.com/cRf3fSS.png)

最後有請教我朋友一些問題，整理相關紀錄。我不是網路工程師，所以這邊筆記會比較簡短易懂，不會有太專業教學內容，所以可能內容有不完整。

<!--more-->

### vLan 概念

簡單來說，就是一台Switch 可以分成兩個 Switch，這樣講很抽像，簡單切出來個 vLan，他們廣播封包不會推到另一個vLan，完成實體切割。當下聽到這個覺得不做 vLan也能做到相同事情？但在安全性就有差了，原因相同網路做可自由更改 IP ，就能做到接到別的網域資料，這有安全性的問題。透過 vLan 可以解決這個問題。


可以參考：[VLAN Explained - YouTube](https://www.youtube.com/watch?v=jC6MJTh9fRE)

## Trunk 概念

網路上看到 Trunk 出去會把 封包加上 tag，進來封包會把 vLan tag 拔掉。

### Trunk 連接的設備

1. Switch 另一接頭也需要設定 Trunk。
2. Router 通過子介面設定，這邊跟 Switch 方式有點不同。

這邊同學給我 Fortigate 50B 防火牆，這邊設備等級類似 Router，所以看到介面沒需要設定 tagged之類的設定。

![](https://i.imgur.com/Y7quiQn.png)

這邊看上面圖沒有設定 Tag 之類。

## Access/Trunk, unTagged/Tagged 名稱

簡單說第一個是 Cisco 術語名稱。(Access / Trunk Mode)。

網路上看到 Trunk 出去會把 封包加上 Tag，進來封包會把 vLan tag 拔掉(unTag)。
因為這邊又講到 Trunk 所以我在貼一次。

名詞是可以轉換的，如下:
Access <=> unTagged
Trunk  <=> Tagged


### unTag / Tagged 對接問題

之前我要看到 Window 可以設定 vLan Tag，我好奇怎麼跟 Switch做串接，假如傳出去 tag 到非 tag會發生什麼事情呢?我在保哥 Blog 裡看到[釐清網管型 Switch 裡與 VLAN + Tag + Untag 之間的觀念 | The Will Will Web](https://blog.miniasp.com/post/2011/02/16/Understanding-Switch-VLAN-Tagged-Untagged)如下:

> Switch 裡預設的情況下所有的 Ports 都是 Untagged VLAN Ports，而所有從 Switch 流入、流出的封包也都應該是 Untagged 的封包，也就是說封包本身並沒有附帶任何 FRAME 以外的資訊，所有無論網管型或一般型的 Switch 都可以認識這種格式的封包。

簡單說，Switch 1 支援 Tag/unTag 設備接到不懂 vLan Switch2 設備，Switch 2 無法識別Switch 1 傳過來封包，會被丟掉(Drop)。tag/unTag 收到 vLan 的 tag/unTag 封包都可以正常讀取風包(這句有點廢話)。不過對接就要非常注意!!首先還是把`網路上看到 Trunk 出去會把 封包加上 Tag，進來封包會把 vLan tag 拔掉(unTag)。`複製一次，在這個機制上 Tag 需要Tag接，說詳一點就是Switch 1 某一Port 設定 Trunk 必須跟 Switch 2 某 Port 設定 Trunk 接，unTag 就跟 unTag 接。

Tagged Port(Trunk) <=> Tagged Port(Trunk)
unTagged Port <=> unTagged Port

上面提到
> 之前我要看到 Window 可以設定 vLan Tag，我好奇怎麼跟 Switch做串接，假如傳出去 tag 到非 tag會發生什麼事情呢?

我這個有問我朋友，他跟我說上面的概念，這個要跟 Tag對接。

[釐清網管型 Switch 裡與 VLAN + Tag + Untag 之間的觀念 | The Will Will Web](https://blog.miniasp.com/post/2011/02/16/Understanding-Switch-VLAN-Tagged-Untagged)，這篇遇到問題是 untag 與 tagged對接問題，整篇篇幅非常長，不過細節還算講得滿清楚，在不太完全了解 Trunk 完全不太懂內容，不過了解後再讀就稍微明白。


## 實際案例

### 模擬案例 - 兩個vLan 共同上網的方法

我原本有推論`vLan 建立 PPPOE，透過兩邊 DHCP 設定gateway 設定 PPPOE 上網 位置`，我朋友說可行的。這邊有簡單化個圖。

![](https://i.imgur.com/rdKzLea.png)

這邊簡單說一下重點

1. PC1、PC2 各放置 Vlan1、Vlan2
2. Switch Trunk 連接到 Router 是用子介面接的
3. A-TUR 是中華電信小烏龜
4. 因為 Router 關係，廣播網愈無法到Switch那邊去，所以PC1、PC2無法做撥號
5. 設定完 DHCP Server 也需要注意是否有用 NAT 動作(SNAT)


DHCP server 是分派IP、DNS、Gateway，**沒有 NAT 功能**。平常我們DHCP設定玩，其他電腦就能上網，其實設備上都幫你做好，不須而外設定，這邊要注意!!


最後我原本想把這兩個vLan接到(Trunk)路由器，但這樣兩邊vlan就能相連了，因為路由器關係。
我朋友建議通常都是用有支援 ACL路由器去做分開網段，Policy 去做網域控管動作。

----

我另一個方案，用 Swtch 建立 虛擬網卡做 PPPOE 連線，但是我同學提點我，Switch 沒辦法像路由器做到建立虛擬網卡，`因為他是Layer 2設備，無法像 Router 建立虛擬網卡`，所以這一招基本上是不行的。


當然還有一些概念沒有提到，像是Native vlan ，我朋友說目前我沒有什麼機會用到，所以這邊我就先不學了。


### 實際案例

![](https://i.imgur.com/h6VWf26.png)

我朋友之前有介紹他家使用 vLan 讓他房間可以用中華電信 Mod方法，大概用vLan。那時候聽看到五彩繽紛顏色沒有很懂，現在回頭看，大概清楚了！透過vLan 弱電箱在客廳，vLan遷過房間去，就可以在房間用中華電信 Mod 看電視。

## 彩蛋

MTR

![](https://i.imgur.com/FCfTp3p.png)
