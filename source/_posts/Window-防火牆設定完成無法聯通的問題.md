---
title: Window 防火牆設定完成無法連通的問題
date: 2021-07-17 22:21:10
tags: [window,firewall]
categories: Window
---

我這台電腦 hfs 無法對外連線，有設定開通防火牆，一直找不出什麼問題。最近看了[Docker Desktop for Windows 啟動的容器無法透過遠端連接的靈異事件簿 | The Will Will Web](https://blog.miniasp.com/post/2021/06/14/Docker-Desktop-for-Windows-Windows-Firewall-Issues)，嘗試著調整防火牆，別台電腦終於可以連了。

雖然保哥紀錄已經夠完整了，建議可以看上面文章，這邊還是紀錄我的筆記。

<!--more-->


## 了解 Window 私有網路/共用網路

什麼是私有網路/共用網路，共用網路在網路芳鄰不會被探索到。私有網路是在家裡讓網路芳鄰可以連到，這邊就不深入探索這些有什麼功能。

怎麼看現在網路用什麼位置?
使用 Powershell 看
```
Get-NetConnectionProfile
```


```
Name             : 網路  2
InterfaceAlias   : nMatrix Virtual Network
InterfaceIndex   : 10
NetworkCategory  : Public
IPv4Connectivity : LocalNetwork
IPv6Connectivity : NoTraffic

Name             : CHTXXX
InterfaceAlias   : 區域連線
InterfaceIndex   : 29
NetworkCategory  : Public
IPv4Connectivity : Internet
IPv6Connectivity : NoTraffic
```

這邊可以看到我是使用共用網路，所以網路芳鄰是找不到的。
上面保哥有特別說，正常在家會想分享網路芳鄰，可能就要開私有網路。在外面要連 Wifi 的話，可以使用共用網路，可以看情況選擇。

### 切換共用網路/私有網路

```powershell
# 私有網路
Set-NetConnectionProfile -Name "CHTXXX" -NetworkCategory Private
# 共用網路
Set-NetConnectionProfile -Name "CHTXXX" -NetworkCategory Public
```

這邊假如你切換網路能跑了，但問題不是出在這邊，你可以往下在看下去是甚麼原因。

## 了解防火牆 取消後發生的事情

一般第一次打開應用程式，如 hfs 會跳出防火牆圖示，如下。

{% asset_img img1.png 防火牆 %}

我們按下「取消」，然後看到防火牆規則

{% asset_img img2.png 防火牆 %}

竟然防火牆規則都是「封鎖」的(私有/共用網路)，然後你打開 hfs 軟體就無法選擇防火牆規則，因為都已經設定封鎖。

簡單做個這個防火牆會發生什麼事情

## 最後整理

{% asset_img img1.png 防火牆 %}

上面有那個選擇，就是對私有網路看共用網路做允許或封鎖設定。

1. 假如你取消，就是私有網路、共用網路做封鎖。
2. 假如你勾選私有網路，那就是私有網路做允許，共用網路做封鎖。
3. 假如你勾選共用網路，那就是共用網路做允許，私有網路做封鎖。
4. 假如全勾，就是私有網路、共用網路做允許。

## 總結

首先，為什麼要知道當前網路是在私有網路/共用網路？因為在看防火牆規則才知道哪一個有效的。
再來就是看當前網路下，防火牆是否開通。

結案:D

## 指令小記

posershell 查詢防火牆規則
```
Get-NetFirewallRule | where { $_.Name -like "*hfs*" } | ft
```

開啟防火牆 cmd `wf.msc`