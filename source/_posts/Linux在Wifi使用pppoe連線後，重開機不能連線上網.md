---
title: Linux在Wifi使用pppoe連線後，重開機不能連線上網
date: 2019-01-27 19:47:29
tags: [wifi, linux]
categories: Linux
---

在使用[Linux 使用 pppoe 連線方法小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/01/26/Linux%E4%BD%BF%E7%94%A8pppoe%E9%80%A3%E7%B7%9A%E6%96%B9%E6%B3%95%E5%B0%8F%E8%A8%98/)後
重開機發現不能上網
我還以為是 wifi 壞掉
好在最後查到原因
在此筆記

<!--more-->

首先，我在`sudo vim /etc/network/interfaces`

```
# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback

iface dsl-provider inet ppp
pre-up /bin/ip link set wlp3s0 up # line maintained by pppoeconf
provider dsl-provider

auto wlp3s0
iface wlp3s0 inet manual
```

在意開始之前，只有這兩段

```
auto lo
iface lo inet loopback
```

~~不過，重開機才能確定網路才能設定到~~
後來網路上找到`sudo service networking restart`
不過我在 ElementaryOS 上沒有什麼效果
後來

```
sudo service network-manager restart
```

發現桌面右上角圖示有重新載入
可正常載入網路狀況

最後發現是`iface wlp3s0 inet manual`這段影響的
不過我還是保留一開始的那兩行
註解掉後還是能用 pppoe 連線

網路設定我就不深入研究了，相關 Linux 設定方法如下

- [Debian / Ubuntu Linux 網路設定範例 | Tsung's Blog](https://blog.longwin.com.tw/2011/02/linux-network-set-example-2011/)
- [Linux 网络接口配置（/etc/network/interfaces） - 邱凯翔 Edward 的个人博客 - CSDN 博客](https://blog.csdn.net/u011077672/article/details/71123319)
- [第 5 章 網絡設置](https://www.debian.org/doc/manuals/debian-reference/ch05.zh-tw.html)
- [葉難: Raspberry Pi：固定私有 IP 與 dhcpcd](http://yehnan.blogspot.com/2016/05/raspberry-piipdhcpcd.html)
- [树莓派 3 设置静态 ip dhcpcd vs /etc/network/interfaces - yjbaobo 的博客 - CSDN 博客](https://blog.csdn.net/yjbaobo/article/details/75146840)


**重新檢查連線**

[小狐狸事務所: 樹莓派自動偵測網路斷線時重開機的方法](http://yhhuang1966.blogspot.com/2018/01/blog-post.html)
[Rebooting the Raspberry Pi when it loses wireless connection | We Work We Play](https://weworkweplay.com/play/rebooting-the-raspberry-pi-when-it-loses-wireless-connection-wifi/)

```
ping -c4 192.168.1.1 > /dev/null
 
if [ $? != 0 ] 
then
  sudo /sbin/shutdown -r now
fi
```

*/5 * * * * /usr/bin/sudo -H /usr/local/bin/checkwifi.sh >> /dev/null 2>&1

```
ping -c4 192.168.1.1 > /dev/null
 
if [ $? != 0 ] 
then
  echo "No network connection, restarting wlan0"
  /sbin/ifdown 'wlan0'
  sleep 5
  /sbin/ifup --force 'wlan0'
fi
```


**2.為了要使raspberry pi  不要進入省電或自動將無線網路關掉**
參考:[IT學習日誌: [Raspberry Pi 2 ModelB] 無線網路常常斷線解決方法](http://learn-infotech-blog.blogspot.com/2015/04/raspberry-pi-2-modelb.html)
在/etc/network/interfaces
加上一行
wireless-power off
 外

修改   /etc/kbd/config 
將 BLANK_TIME= 改為 BLANK_TIME=0 
和 POWERDOWN_TIME=  改為  POWERDOWN_TIME=0 