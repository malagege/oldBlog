---
title: Raspberry PI 3B設定wifi連線方法
date: 2018-11-19 22:08:29
tags:
categories:
---

最近衝了PI 3，因為沒有多的網路線
目前簡單做個wifi設定筆記

<!--more-->

## 設定ssh
不知道哪個版本就開始要放置sd檔案上面放`ssh`檔案(touch ssh)
開機才會自動開啟ssh server

當然有上次經驗，我猜想wifi設定應該也有設定的地方
google一下，果然不出我所料


## 設定wifi

boot磁區新增一個`wpa_supplicant.conf`檔案，內容如下
```
country=TW
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="SSID_NAME"
    psk="SSID_KEY"
    key_mgmt=WPA-PSK
    scan_ssid=1
}

```

原理如下
```
請注意！！！
如果是用 Windows 的朋友，千萬不要用「記事本」去編輯，它編出來的純文字檔，在 Linux 讀取會有問題，請改用 Sublime Text、EmiEditor、Notepad++ .... 等支援 Linux 格式的編輯器來編輯。

該輸入的資料全部輸入完畢之後，別忘了存檔，而且要正確的把記憶卡退出電腦，可別直接拔了就走。

接著只要把記憶卡插上 Raspberry Pi 上開機就行了，Rasbian 會把 /boot/wpa_supplicant.conf 複製到 /etc/wpa_supplicant/ 目錄裡，然後就會去讀取裡面的設定值，自動連上無線基地台了。
```
參考:[Raspberry Pi 的基礎 - 沒螢幕、沒鍵盤、沒網路，居然也能設定 Wi-FI 連線 | IT 技術家](http://blog.itist.tw/2017/03/headless-wifi-setup-without-monitor-keyboard-or-cables-for-raspberry-pi-zero-w-and-3-model-b.html)
[邊玩邊學，輕鬆自學 칸: [樹莓派][Raspberry Pi] 樹莓派無頭式安裝教學（一）- wifi + ssh](https://khanwhlee.blogspot.com/2017/05/wifi-ssh.html?fbclid=IwAR1oGeb690-RkyXFatZM__lvmOc4DrnstVDEfMDLeWGnSrPcrX0TMarjNCk)

## 要怎麼知道IP

登入中華電信小烏龜看IP，發現中華電信P883竟然改IP密碼
可能是為了安全性...
P883帳號密碼為cht/p883(+mac後面四碼)
怎麼看到mac呢
網路上說可以登入user/user進去看mac
另外一種是`arp -a`我自己想的
有機會看到
登入p883就能知道IP

最後，只要有開ssh跟wifi後樹梅派接電就能ssh控制使用，真的很方便
留著下次用的時候再翻閱XD