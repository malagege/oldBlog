---
title: Window 確認根憑證
date: 2022-02-13 23:56:36
tags: [ssl,tls,window,憑證]
categories: window
---

最近我朋友在工作遇到憑證問題，之前有幾次研究憑證，幫我朋友看看憑證問題。
<!--more-->

相關連結:
* [產生自簽憑證筆記 | 程式狂想筆記](https://malagege.github.io/blog/2020/07/18/%E7%94%A2%E7%94%9F%E8%87%AA%E7%B0%BD%E6%86%91%E8%AD%89%E7%AD%86%E8%A8%98/)
* [設備根憑證不正確導致不能打API問題 | 程式狂想筆記](https://malagege.github.io/blog/2020/07/14/%E8%A8%AD%E5%82%99%E6%A0%B9%E6%86%91%E8%AD%89%E4%B8%8D%E6%AD%A3%E7%A2%BA%E5%B0%8E%E8%87%B4%E4%B8%8D%E8%83%BD%E6%89%93API%E5%95%8F%E9%A1%8C/)
* [Java 因憑證 SNI 問題驗證不過 | 程式狂想筆記](https://malagege.github.io/blog/2020/06/25/Java-%E5%9B%A0%E6%86%91%E8%AD%89-SNI-%E5%95%8F%E9%A1%8C%E9%A9%97%E8%AD%89%E4%B8%8D%E9%81%8E/)
* [SSL Pinning 和 HSTS 小記 | 程式狂想筆記](https://malagege.github.io/blog/2020/06/18/SSL-Pinning-%E5%92%8C-HSTS-%E5%B0%8F%E8%A8%98/)
* [Linux 指令確認SSL憑證 | 程式狂想筆記](https://malagege.github.io/blog/2020/05/30/Linux-%E6%8C%87%E4%BB%A4%E7%A2%BA%E8%AA%8DSSL%E6%86%91%E8%AD%89/)
* [萬用憑證(WildCard)和中間憑證小記 | 程式狂想筆記](https://malagege.github.io/blog/2018/10/10/%E8%90%AC%E7%94%A8%E6%86%91%E8%AD%89-WildCard-%E5%92%8C%E4%B8%AD%E9%96%93%E6%86%91%E8%AD%89%E5%B0%8F%E8%A8%98/)

這次要簡查window 憑證，我都忘記要怎麼看，稍為記錄一下

<!--more-->

## 問題

IE 正常，Chrome 不正常。

![](https://media.discordapp.net/attachments/550308216911822865/907557024945037352/image0.jpg?width=1802&height=1352)

![](https://i.imgur.com/oSsGxHA.jpg)


## 查看 Windows 憑證

1. window 打開憑證中心

```
certlm.msc
```

![](https://i.imgur.com/71tNdxw.png)


或者`mmc` 參考設定[如何：使用 MMC 嵌入式管理單元來查看憑證 - WCF | Microsoft Docs](https://docs.microsoft.com/zh-tw/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)



2. 查看 TWCA 根憑證

![](https://i.imgur.com/O1evw4w.png)




查看這邊TWCＡ憑證是不是有三個? 其中最重要的是有沒有`TWCA Global Root CA`，如果沒有的話，需要按裝TWCA憑證，

![](https://i.imgur.com/Ys6BB5S.png)

假如有的話，再看`憑證指紋`是否為這一串

```
9cbb4853f6a4f6d352a4e83252556013f5adaf65
```

3. 再回到 Chrome 看網頁憑證

![](https://i.imgur.com/L6TySqY.png)

![](https://i.imgur.com/iiDbBBo.png)


![](https://i.imgur.com/iL1UR2R.png)


4. 比對電腦憑證是否有該網站根憑證

![](https://i.imgur.com/jZbP0qu.png)


備註: 每個瀏覽器看到憑證串接會不一樣，像剛剛範例是四層。Firefox看是三層。

![](https://i.imgur.com/f1tPzVY.png)
 
 
 ## 解決方法記錄
 
 ![](https://i.imgur.com/OmyoNnb.png)


![](https://i.imgur.com/W5MfMMU.png)


最後我朋友刪掉舊的根憑證就解決問題了。


![](https://i.imgur.com/lfTrTUl.png)


最後我朋友說 CHUNGHWA(中華電信) 比 TWCA 好，哈哈。



## 目前猜測(結論)

我朋友 TWCA憑證都有更新到，但舊的憑證竟然還在?所以造成影響?
不知道是不是 Windows 憑證同步問題?但就算舊憑證過期，其他正確根憑證應該還是能串到才對。
所以 Chrome 遇到這個問題簡查一下這個問題。