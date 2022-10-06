---
layout: post
title: '[Arduino]新手用 Wifi 晶片 ESP8266 和 USB TTL Serial(PL2303)接上PC'
date: 2016-06-24 16:33
comments: true
categories: Arduino
tags: ['Arduino']
---
![thumb.jpg](http://user-image.logdown.io/user/8284/blog/8171/post/745009/bsmdGiIZTWm0zhuttVIV_thumb.jpg)
最近同事揪來 ~~玩~~ 學Arduino
由於之前忘記採購到WIFI模組
同事最近燒壞了WIFI模組(好像接到5v線)
最近借我了ESP8266和PL2303，叫我好好的玩
~~那我就大力的玩下去~~
當然我就簡單找網路上實作
<!--more-->



前面參考[*教學*Arduino WIFI(ESP8266) 初次上手應用](http://lolwarden.pixnet.net/blog/post/82031214)

一開始接wifi接到TTL真的不習慣，好不容易照上面教學做完
花了一些時間，接上電腦上面...竟然找不到驅動
~~這之前我完全不知道那個USB是TTL又花了一些時間  囧~~
中間還滿想衝Webduino就不會有這些問題
有興趣的可以看一下這個[Marty x Will @ 前端工程師的逆襲：使用 Web 技術玩轉 Arduino 開發版 ](https://www.youtube.com/watch?v=P2V2d4aDP8w)
用win7搜尋TTL驅動[PL2303](http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=225&pcid=41)
驅動程式就看到

接下來就繼續[*教學*Arduino WIFI(ESP8266) 初次上手應用](http://lolwarden.pixnet.net/blog/post/82031214) Realterm(終端機)

![2016-06-25_171933.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/zIGKuSYTYyeon44pTLyv_2016-06-25_171933.png)

CH_PD和VCC連在一起要接3v，不然接到5v會有燒掉的風險

照上面教學還是連不到，我就以為接錯線....
馬上又重看才發現TXD<--->RXD要對接
所以就重新拔掉重接線

接線發現TTL變白燈(事後才發現這好像才是插錯線)
我~~興奮啦~~ 感覺這次亮白燈應該就接對線了

但還是試了很久還是失敗orz   最後發現wifi沒有亮
我還以為是esp8266燒壞了  囧

最終爬掉在重接，TTL亮紅燈 wifi也有亮燈
就可以用了

[如何燒錄 ESP8266 無線模組的韌體](http://ruten-proteus.blogspot.tw/2014/12/esp8266-firmware-burning.html)
[ESP8266 更新韌體 ](http://yhhuang1966.blogspot.tw/2015/08/esp8266.html)
才發現GPIO要接GNP才能進入燒寫模式(一直以為就跟手機刷機一樣XD)
還要燒四次
置於他說要額外接上電源，這樣電力才會夠
但我是用筆電就可以燒入成功

在網路爬文後，需要更新ESP8266韌體才能玩Arduino
[ESP8266 firmware 燒寫介紹 ](https://www.youtube.com/watch?v=bq71JX25DGg)

從上面官網可以看到[ESP8266Flasher.exe](http://code.unumobile.com/wf8266r/tutorials/esp8266/00_FM)軟體
燒韌體還是失敗

![2016-06-25_173420.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/rkkryLTbKKDr3j3vTADS_2016-06-25_173420.png)
還要抓四個韌體更新[也在這個網站裡的Google Driver](http://code.unumobile.com/wf8266r/tutorials/esp8266/00_FM)
照著Google Driver文件設定為8MB
![2016-06-25_173906.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/tdf9BsPRkKJtedCuRMsA_2016-06-25_173906.png)
在設定Config **4個設定檔(其中一個一樣) 和 address**
![2016-06-25_173837.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/d8NBaxCfTzSNZLtEj6ls_2016-06-25_173837.png)
點Flash進行燒入，燒入的過程中看到有MAC就表示有成功
再設定Config把第一個關掉
![2016-06-25_173929.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/67UV2S53S96vSgIEytjZ_2016-06-25_173929.png)
再回去點Flash(重覆4次)就完成


開啟Realterm 鮑率打9600
最後要打`115200`
網路查了一下，舊版好像是9600，新版才是115200
要更新韌體才能用Arduino
但要手動改9600才能用
拍一下要設定的地方

![2016-06-25_174508.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/wnVWEXwTSnuqTURcWrcw_2016-06-25_174508.png)
![2016-06-25_174737.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/hhYoQutSTR2fZBIl2FnY_2016-06-25_174737.png)
OPEN是點掉再按下去
![2016-06-25_175119.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/mstYMzMTDWXz7ACw4C2l_2016-06-25_175119.png)

```
AT

OK
```
感動

最後發現我常用的pietty也能連
打指令要用`Enter`後再按`Ctrl+J`
![2016-06-25_175914.png](http://user-image.logdown.io/user/8284/blog/8171/post/745009/PYJ2giwpTJu2hWAUUqLY_2016-06-25_175914.png)
好像這個比較方便耶XD

下次用用Arduino連ESP8266



參考來源：
http://ruten-proteus.blogspot.tw/2014/12/esp8266-firmware-burning.html
http://lets-make-games.blogspot.tw/2015/04/wifi-esp8266-wifi.html
https://mlwmlw.org/2015/07/%E6%B7%B1%E5%85%A5%E6%B7%BA%E5%87%BA-wifi-%E6%99%B6%E7%89%87-esp8266-with-arduino/
https://www.youtube.com/watch?v=bq71JX25DGg
http://ruten-proteus.blogspot.tw/2014/12/esp8266-at-command.html
http://flower-platform.com/2015/12/16/esp8266-with-at-commands-connect-from-pc-with-putty/
http://lets-make-games.blogspot.tw/2015/04/wifi-esp8266-wifi.html
http://code.unumobile.com/wf8266r/tutorials/esp8266/00_FM
http://www.prolific.com.tw/US/ShowProduct.aspx?p_id=225&pcid=41
https://docs.google.com/viewer?a=v&pid=sites&srcid=a3N2cy5raC5lZHUudHd8dGpofGd4OjFiNWM2NGJjMjA4NmZlZjA
https://github.com/nodemcu/nodemcu-flasher/tree/master/Win64/Release
http://yhhuang1966.blogspot.tw/2015/08/esp8266.html