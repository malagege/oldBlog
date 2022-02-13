---
title: Java 遇到 connection timed out 問題
date: 2022-02-13 23:00:45
tags: [java, timeout]
categories: Java
---

## 遇到問題

最近公司發現發 LINE 推播時候發現 log 會有失敗 Exception，仔細看了一下訊息寫`connection timed out`，到底為什麼發生這個問題，是系統？是網路？是路由器？由於不是100%會發生，最好狀況沒發生，最高一天發生6次失敗，一天的量最多大概200多，所以失敗機率雖低，但這個遇到的情況還滿高的。這邊我蒐集一些方法，順便紀錄一下。

<!--more-->

### 函示庫問題
程式出現 connection timed out，有查看程是Exception 原因，可參考[淺談Java中的TCP超時](https://hoswey.github.io/2019/07/23/%E6%B5%85%E8%B0%88Java%E4%B8%AD%E7%9A%84TCP%E8%B6%85%E6%97%B6/)。
程式建立TCP連線時候，沒有收到ACK回應，導致connection timed out。
我們程式使用Line 開發Java 推播函示庫，裡面原始碼就有設定 timeout 10秒鐘(程式連結)，因為連線超過10秒鐘沒有回應，所以就發生 connection timed out。
我們測試連線大概有4%機率會發生超過10秒鐘或連線失敗。

![](https://i.imgur.com/CT3uhEK.png)


TODO: 

- [淺談Java中的TCP超時 | 程序員，川流不息](https://hoswey.github.io/2019/07/23/%E6%B5%85%E8%B0%88Java%E4%B8%AD%E7%9A%84TCP%E8%B6%85%E6%97%B6/)

## 找到排除方法

### TCP 超量

- [linux上TCP connection timeout的原因查找_沧海一粟-CSDN博客_linux tcp timeout](https://blog.csdn.net/raintungli/article/details/37879907)
- [一次HTTP connect-timeout的排查(上) - 简书](https://www.jianshu.com/p/3b9c4216b822)
- [彻底理解connection timeout - 簡書](https://www.jianshu.com/p/d5060b16650e)


這邊我原本也滿擔心，因為我們公司是多個團隊用共用主機，想說別的專案會有大量呼叫 API。不過我看使用量好像還好，大概 100 建立多個連線，大部分專案沒有做外連動作，所以排除掉。

可參考[Windows / Linux 如何查詢連線數量(connections & sessions) - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10045673)指令:
```bash=
# Linux:
# 看全部: 
netstat -aunt | wc -l
# 只看 TCP: 
netstat -ant | wc -l
# 只看 UDP: 
netstat -aut | wa -l

# Windows:
# 只看TCP: 
netstat -an | find "TCP" /c
# 只看UDP: 
netstat -an | find "UDP" /c
```

### MTR 測試

因為最近剛好問我朋友 vLan 問題，剛好順便問我朋友也是做網路相關工作，他有先試著叫我做`ping`主機動作，不過我有問我大量`ping`情況會發生，有什麼方法看網路這一塊哪邊問題，我朋友跟我講一個工具，`MTR`工具，他們通常會請客戶端測試用。


![](https://i.imgur.com/zwM7eQD.png)

上圖，倒數第二跳好像滿高的!!不過我朋友說，不一定有問題，因為正常中間 loss 很多的話最後應該也會滿多，推測**我們公司的 ISP 選的路線可能非最佳路線**，像他幫我測試用中華電信 ISP 線路 `PING` 沒有這麼高(如下圖)，所以問題應該是出在ISP和 LINE 雲端服務商機率比較大。

![](https://i.imgur.com/G4pXPUX.png)


我朋友提供一些方案，因為我們 ISP 公司不一樣，所以解析出來 DNS 的 IP 也不一樣，所以有建議我改 host 檔案指定中華 DNS 出來 IP，可以先看打API會有掉包問題，不過目前我應該沒有這個權限，我有先請我們公司同仁先看，後續有消息會再補上。不過我有先MTR到中華解析出來 IP ，ping還滿好的。

![](https://i.imgur.com/ZCMJnHy.png)

經典聊天紀錄留一下。
![](https://i.imgur.com/0eZk9nQ.png)

網路這一塊滿深奧XD。

## Line 主要找到 timeout 設定地方

預設 10 秒會 timeout。

[line-bot-sdk-java/LineMessagingClientBuilder.java at cbcb096023f2de84684751839656a0c458f6ee41 · line/line-bot-sdk-java · GitHub](https://github.com/line/line-bot-sdk-java/blob/cbcb096023f2de84684751839656a0c458f6ee41/line-bot-api-client/src/main/java/com/linecorp/bot/client/LineMessagingClientBuilder.java)


[line-bot-sdk-java/LineClientConstants.java at cbcb096023f2de84684751839656a0c458f6ee41 · line/line-bot-sdk-java · GitHub](https://github.com/line/line-bot-sdk-java/blob/cbcb096023f2de84684751839656a0c458f6ee41/line-bot-api-client/src/main/java/com/linecorp/bot/client/LineClientConstants.java)