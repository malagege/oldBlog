---
title: Linux使用pppoe連線方法小記
date: 2019-01-26 22:55:39
tags: [pppoe, network]
categories: Linux
---

最近在好奇 pppoe 到底能不能走 wifi
google 一下發現[[問題] 走 Wi-Fi 可以搭配 PPPoE 嗎? - 看板 NextTV - 批踢踢實業坊](https://www.ptt.cc/bbs/NextTV/M.1338039177.A.D3D.html)
這篇有人說可以也有人說不行...
其中一段`cassine:PPPoE顧名思義就是PPP over Ethernet，不是PPP over WiFi`
感覺還滿有道理的
不過還是有看到留言說可以使用
今天要來破解是不是真的...

<!--more-->

好啦，突然想到家裡有一台 surface
直接連 wifi 做 pppoe 連線就可以馬上知道結果
結果**可以**
但是不知道 Linux 能不能用?

簡單記錄一下怎麼使用

```bash
# 安裝pppoeconf
sudo apt-get install pppoeconf
# 設定
sudo pppoeconf
```

設定可以參考[沉思的伊文: Raspberry Pi 樹莓派 ADSL PPPoE 連線到網際網路](http://thinker-evans.blogspot.com/2015/04/raspberry-pi-adsl-pppoe.html)
'noauth'跟'defaultroute'這個選 yes

```
向配置文件添加 『noauth' 和 『defaultroute'
noauth 不認證遠端身份，家庭寬帶默認 Yes 即可
defaultroute 添加默認路由，不手動改路由表或者特殊需求默認 Yes 即可
參考[【家庭服务器计划】pppoeconf | Debian 配置 PPPOE 上网 – Lensual‘s Space](https://lensual.space/brain-hole/846.html)
```

[linux 添加静态路由 - leon - CSDN 博客](https://blog.csdn.net/moreorless/article/details/5397427)
MTU 選項也選 yes
詳細:[小議 TCP 的 MSS(最大分段)以及 MTU - 王朝網路 - wangchao.net.cn](http://tc.wangchao.net.cn/bbs/detail_1450004.html)
[【家庭服务器计划】pppoeconf | Debian 配置 PPPOE 上网 – Lensual‘s Space](https://lensual.space/brain-hole/846.html)
裡面都詳細提到

```
MTU 最大傳輸單元，二層幀的淨荷
MSS 最大報文長度，TCP 協議特性，TCP 協議的淨荷，用於協商適應路徑中的 MTU
Ethernet 的 MTU 為 1500 (Byte) ，超過 1500 的載荷 ( payload ) 將被分段，最大幀長度為 Eth Header(14) + Payload(1500) + FCS(4) = 1518 (Byte)。其實 FCS 用 wireshark 抓包看不到的，用於校驗已經被系統底層剔除。另外以太網規範還有前導碼和幀間隔，那些是用來底層收發控制相關的（誤
PPP 的 MTU 同為 1500，最大幀長度為 PPP Header(5) + Payload(1500) + FCS(2) + Flag(1) = 1508 (Byte)。
要把 PPP 塞進 Ethernet 就是 PPPOE，需要把 PPP 的 MTU 縮減才可以使得 PPP 滿足 Ethernet 的 MTU。
原來的 PPP 最大會出現 1508 的幀，現在需要控制在 1500 以內。PPP 的 MTU 就是 1500 – (1508 – 1500) = 1492。
所以 PPPOE 的 MTU 通常為 1492 (Byte) ，以便在 MTU 為 1500 的 Ethernet 上運行。
MSS 箝制(Clamping) 會修改 TCP SYN 的 MSS 以符合當前的 MTU。因為置於路由器後面的主機使用的是 MTU 為 1500 的 Ethernet 。雖然 ICMP 協議可以在傳輸過程中修正 TCP 的 MSS ，但是部分運營商和設備考慮到安全問題或者就是不想讓你 ping 和 tracertoute 免得投訴他們禁止了 ICMP 協議。
```

基本上設定都是 yes

連線

```bash
# 連線dsl-provider設定檔
pon dsl-provider
# 斷線pppoe
poff dsl-provider
# 查詢狀態
plog
ifconfig
# 關閉全部pppoe
poff -a
```

```
PPPOE設定檔
    /etc/ppp/peers/dsl-provider
    dsl-provider內紀錄ADSL連線相關參數（含帳號），可以修改成hinet以利識別，但改檔名後，pppoe_on_boot檔內的開機自動撥接的設定檔名稱也要跟著換，否則開機自動撥接功能會失效。
    /etc/ppp/pppoe_on_boot
    設定系統開機時自動撥接之設定檔，其中「exec pppd call dsl-provider」就是執行撥接時呼叫的設定檔
```

重新連線 script[Debian Linux: ADSL PPPoE 自動重撥 Script | Tsung's Blog](https://blog.longwin.com.tw/2007/08/adsl_repppoe_script_2007/)

```
重新撥接的程式 及 設定步驟

重新撥接的程式如下: (vim /usr/bin/repppoe)

    #!/bin/bash
    #if !ping -c 3 168.95.1.1 > /dev/null 2>&1
    #if !(cat /proc/net/dev | grep ppp0) > /dev/null 2>&1
    #if !(cat /proc/net/dev | grep ppp) > /dev/null 2>&1
    if !ping -c 3 tw.yahoo.com > /dev/null 2>&1
    then
    #/usr/bin/poff
    #/usr/bin/pon dsl-provider
    /usr/bin/pon
    fi

chmod +x /usr/bin/repppoe
設定 crontab (設 root 的 crontab)

    crontab -e # 之後寫入下述
    */5 * * * * /usr/bin/repppoe
```

進入就到這邊，以後要用可以翻翻這篇。

小記...，發現 pon 可以一直新增 pppoe 連線
但好像最後面新增會做 default route
所以只會有一個 ip 才會連到
我一直以為都可以連...
但還是要小心，我記得 hinet 只能撥接 8 個
[HiNet 客戶服務 > 問題搜尋 > 上網問題](http://service.hinet.net/2004/adslstaticip_faq.htm)

參考來源:

- [沉思的伊文: Raspberry Pi 樹莓派 ADSL PPPoE 連線到網際網路](http://thinker-evans.blogspot.com/2015/04/raspberry-pi-adsl-pppoe.html)
- [在 Linux 上拨号上网(PPPoE) | 光阴的故事](https://vvl.me/2017/09/30/Linux-PPPoE/)
- [在 CentOS 7 設定 PPPoE 撥接功能 | Kevin Linul 網路日記本](http://blog.kevinlinul.idv.tw/?p=174)
- [鳥哥的 Linux 私房菜 -- 連上 Internet 的方法與步驟](http://linux.vbird.org/linux_server/0130internet_connect.php#connect_adsl)
- [Ubuntu 網路設定 - PPPoE (ADSL) 指令 | 凍仁的筆記](http://note.drx.tw/2008/08/networkpppoe-adsl.html)
- [【家庭服务器计划】pppoeconf | Debian 配置 PPPOE 上网 – Lensual‘s Space](https://lensual.space/brain-hole/846.html)
- [【Linux】pppoe 網路撥接設定 @ 等待 :: 痞客邦 ::](http://joe01032002.pixnet.net/blog/post/92428377-%E3%80%90linux%E3%80%91pppoe%E7%B6%B2%E8%B7%AF%E6%92%A5%E6%8E%A5%E8%A8%AD%E5%AE%9A)
- [設定 ADSL（PPPOE）連線 | Gavaee's Blog](https://gavaee.wordpress.com/2011/10/21/%E8%A8%AD%E5%AE%9Aadsl%EF%BC%88pppoe%EF%BC%89%E9%80%A3%E7%B7%9A/)
- [Debian Linux: ADSL PPPoE 自動重撥 Script | Tsung's Blog](https://blog.longwin.com.tw/2007/08/adsl_repppoe_script_2007/)
