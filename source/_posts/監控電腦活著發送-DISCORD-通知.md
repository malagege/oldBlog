---
title: 監控電腦活著發送 DISCORD 通知
date: 2020-06-27 18:47:43
tags: [discord, raspberry]
categories: Linux
---

最近 Raspberry Pi 三號機網路常常掛點
雖然之前有找到讓它重新開機的方法
[Linux在Wifi使用pppoe連線後，重開機不能連線上網 | 程式狂想筆記](https://malagege.github.io/blog/2019/01/27/Linux%E5%9C%A8Wifi%E4%BD%BF%E7%94%A8pppoe%E9%80%A3%E7%B7%9A%E5%BE%8C%EF%BC%8C%E9%87%8D%E9%96%8B%E6%A9%9F%E4%B8%8D%E8%83%BD%E9%80%A3%E7%B7%9A%E4%B8%8A%E7%B6%B2/)
但我沒採用這個方案
畢竟有時候需要確保有哪些程式正在執行
再重開機是比較好的

<!--more-->

## 有問題電腦解決重新連線方法

最近看到[Headless raspberryPi with eth0+USB issue Register 0x00000114 - Raspberry Pi Forums](https://www.raspberrypi.org/forums/viewtopic.php?t=19971)
這邊會 ping 自己的路由
失敗會重啟 Interface

```bash
#!/bin/sh

# cron script for checking wlan connectivity
IP_FOR_TEST="192.168.1.1"
PING_COUNT=1

PING="/bin/ping"
IFUP="/sbin/ifup"
IFDOWN="/sbin/ifdown --force"
INTERFACE="eth0"

FFLAG="/opt/check_lan/stuck.fflg"

# ping test
$PING -c $PING_COUNT $IP_FOR_TEST > /dev/null 2> /dev/null
if [ $? -ge 1 ]
then
    logger "$INTERFACE seems to be down, trying to bring it up..."
        if [ -e $FFLAG ]
        then
                logger "$INTERFACE is still down, REBOOT to recover ..."
                rm -f $FFLAG 2>/dev/null
                sudo reboot
        else
                touch $FFLAG
                logger $(sudo $IFDOWN $INTERFACE)
                sleep 10
                logger $(sudo $IFUP $INTERFACE)
        fi
else
#    logger "$INTERFACE is up"
    rm -f $FFLAG 2>/dev/null
fi
```
但我不想重開機
之後找時間改寫

### 改成監控紀錄 log

```bash
#!/bin/sh

# cron script for checking wlan connectivity
IP_FOR_TEST="192.168.1.1"
PING_COUNT=1

PING="/bin/ping"
INTERFACE="eth0"

# ping test
$PING -c $PING_COUNT $IP_FOR_TEST > /dev/null 2> /dev/null
if [ $? -ge 1 ]
then
    logger -it check_interface "============================="
    logger -it check_interface "$INTERFACE seems to be down, trying to bring it up..."
    logger -it check_interface "carrier:"
    cat /sys/class/net/eth0/carrier | logger -it check_interface
    logger -it check_interface "operstate:"
    cat /sys/class/net/eth0/operstate | logger -it check_interface
    logger -it check_interface "route -n"
    route -n 2>&1 | logger -it check_interface
    logger -it check_interface "ifconfig:"
    ifconfig 2>&1 | logger -it check_interface
    logger -it check_interface "ping:"
    ping -c4 192.168.1.1 | logger -it check_interface
    ping -c4 8.8.8.8 | logger -it check_interface
    logger -it check_interface "============================="
fi
```

紀錄 log
[linux日志logger命令详解_飘过的春风-CSDN博客_linux logger](https://blog.csdn.net/u011630575/article/details/52055116)
[Linux 如何將資料寫到 Syslog | Tsung's Blog](https://blog.longwin.com.tw/2011/11/linux-data-syslog-logger-2011/)
[shell - Linux : how to redirect stdout & stderr to logger? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/124455/linux-how-to-redirect-stdout-stderr-to-logger)

相關 interface 資訊
[如何在 Linux Kernel 下偵測網路的連線狀態？](http://rainstingtw.blogspot.com/2014/08/how-to-detect-ethernet-connected-state.html)
[wifi连接中operstate和carrier分别是什么作用？-CSDN论坛](https://bbs.csdn.net/topics/390495032)
[在Linux上，如何檢測物理電纜是否已連接到網卡插槽 - Ubuntu問答](https://ubuntuqa.com/zh-tw/article/7995.html)
[树莓派折腾日记 二：网络相关配置 | 冰棒实验室](https://blog.beanbang.cn/2019/05/17/raspberry-diary-02-network-configure/)

## 監控電腦活著發送 DISCORD 通知

其他機都非常穩定
別台監控狀態

```bash
#!/bin/bash
PING="/bin/ping"
PING_COUNT=1

IP=(
192.168.1.1
192.168.1.2
192.168.1.3
)
DSCORD_WEBHOOK="https://discordapp.com/api/webhooks/*"

function sendDiscordNotice(){
        local MSG=$2
        # echo $MSG
        local URL=$1
        curl -H "Content-Type: application/json" \
        -X POST \
        -d "{\"username\": \"system\", \"content\": \"${MSG}\"}" $URL
}

#sendDiscordNotice $DSCORD_WEBHOOK "test"
for i in "${IP[@]}"
do
    #echo $i
    MSG="${i} 沒法 PING 到主機，請管理員查詢狀況"
    #echo $MSG
    $PING -c $PING_COUNT $i > /dev/null 2> /dev/null
    if [ $? -ge 1 ]; then
        sendDiscordNotice "${DSCORD_WEBHOOK}" "${MSG}"
    fi
done 

```


這邊學到shell function 參數是用 $1 $2
參數最後外面加個`"`預防參考帶進去有問題

[Bash 程式設計教學與範例：function 自訂函數 - Office 指南](https://officeguide.cc/bash-tutorial-define-use-functions/)

對 variable 使用 foeach

[Loop through an array of strings in Bash? - Stack Overflow](https://stackoverflow.com/questions/8880603/loop-through-an-array-of-strings-in-bash)

## 番外

### 開機做發信動作

[ubuntu server 開機自動發信通知(使用gmail) | pupuliao的部落格](https://www.pupuliao.info/2013/06/ubuntu-server-%E9%96%8B%E6%A9%9F%E8%87%AA%E5%8B%95%E7%99%BC%E4%BF%A1%E9%80%9A%E7%9F%A5%E4%BD%BF%E7%94%A8gmail/)

決得這邊沒有考慮到網路的問題
但這個方法也滿有用的
先記錄

## 看到另類 ping 大法

剛好爬到不錯文章，想到之前有類似做這個，可以留著以後參考

[为 ping 添加时间戳后台运行实时输出保存日志 | HelloDog](https://wsgzao.github.io/post/ping/) [備份圖](https://i.imgur.com/TQAsvkP.png)
[为ping添加时间戳后台运行实时输出保存日志 - 知乎](https://zhuanlan.zhihu.com/p/379244697)[備份圖](https://i.imgur.com/xXwkWbg.png)

