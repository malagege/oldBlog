---
title: 使用 iptables 實做 NAT 轉發封包
date: 2020-06-14 17:38:42
tags: [linux, nat ,iptables]
categories: Linux
---

之前有跟別的公司合作取得三組 VPN 帳號密碼
但是我們團隊有 10 人
這三組怎麼做
雖然最近專案已經結束了
我有看到使用 Linux iptables 設定 NAT 轉發封包
今天就簡單嘗試

<!--more-->

## 網路知識

~~臨時抱佛腳時間~~

### 預設閘道

一般我的常設定 IP ，都會設定預設閘道
預設閘道通常都是設定中華電信小烏龜
~~一般可能不會去了解原因~~
這邊簡呆說明一下原因
之前幾篇有講到 CIDR
現在正常設定 IP 都會設定 Mask (網路遮罩)
只要超過遮罩時候，**就會轉發到 預設閘道**
就會根據路由器做轉發到別的網域

### NAT

wifi 連線的時候，會自動分配 IP (DHCP)
通常拿到的 IP 都是私有網域 IP
所以外面的網路通常 PING 不到

#### SNAT

[鳥哥的 Linux 私房菜 -- Linux 防火牆與 NAT 伺服器](http://linux.vbird.org/linux_server/0250simple_firewall.php#fig9.1-2)

#### DNAT

一般常用的就是 Virtual Server，這就是一種 DNAT


[鳥哥的 Linux 私房菜 -- Linux 防火牆與 NAT 伺服器](http://linux.vbird.org/linux_server/0250simple_firewall.php#fig9.1-3)

### 其他小記

#### Windows 10 和 Linux 如何轉發 IP

每一台電腦都可以擔任轉發動作，但一開始我在**預設閘道**怎麼沒有效果
其實都需要設定

```
PS: 小補轉發跨網域會發生什麼事情
一般轉發不會改變來源 IP
跨網域後封包不會送回來
```

Win 10

> 2、windows xp、Vista、windows7：
> 
> 打开注册表：regedit.exe
> 
> 打到：HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters
> 
> 增加：
> Value Name: IpEnableRouterData Type: REG_DWORD
> 
> Value: 1
> 
> 重启机器。
> ————————————————
> 版权声明：本文为CSDN博主「kl222」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/kl222/java/article/details/12878755

[打开windows的路由功能_康林工作室-CSDN博客](https://blog.csdn.net/kl222/article/details/12878755)

linux

一般在設定 iptables forward
但前置作業記得要先做這個動作

> 查詢IP forward功能是否開啟
> cat /proc/sys/net/ipv4/ip_forward
> 0代表沒有啟動
> 1代表已啟動
> 
> 暫時啟動IP forward功能
> echo 1 > /proc/sys/net/ipv4/ip_forward
> 重新開機就會失效
> 
> 永久開啟IP forward功能
> vi /etc/sysctl.conf
> 加上這句
> net.ipv4.ip_forward = 1
> 立刻讓設定生效
> sysctl -p

[IP forward @ 小志的隨便記 :: 痞客邦 ::](https://danshuei.pixnet.net/blog/post/119262469-ip-forward)

## 如何 SNAT 轉發

不錯文章
- [Linux ip forward - sparkdev - 博客园](https://www.cnblogs.com/sparkdev/p/9262825.html)
- [如何在 Linux 下利用 iptables 快速設定 NAT 環境 | The Will Will Web](https://blog.miniasp.com/post/2010/06/08/Setup-NAT-environment-using-iptables-under-Linux)


我這邊簡單實作讓另外一台沒網路用預設閘道連線
參考保哥範例如下

NAT(PC):
wlp3s0: 192.168.1.117 (可以連線出去網段)
wlp3s0: 192.168.100.2 （環境要做 NAT 網段)

PC(一般):
IP: 192.168.100.50(起設定在 192.168.100.0/24 範圍內)
Mask: 255.255.255.0
Gateway: 192.168.100.2

```bash
# 1. Define variables
## WAN_IF 是設定轉發出去 IP & 從哪邊轉發出去
## 我這邊是 Linux 在 1 個 Wifi Interface 做 2 個 IP
## 這邊我主要是對另一台做連線動作
## 保哥範例是 PPPOE ，這邊可以設定 VPN Interface

WAN_IF="wlp3s0"
WAN_IP="192.168.1.117"

## 進來 IP 做轉發，通常是設定區域網路 IP 範圍
## 這邊我也是用同一個 Interface

LAN_IF="wlp3s0"
LAN_IP="192.168.100.0/24"


# 2. modprobe

/sbin/modprobe ip_tables
/sbin/modprobe iptable_filter
/sbin/modprobe ip_conntrack
/sbin/modprobe ip_conntrack_ftp
/sbin/modprobe ip_conntrack_irc
/sbin/modprobe ipt_state
/sbin/modprobe ipt_limit
/sbin/modprobe ipt_REJECT
/sbin/modprobe ipt_REDIRECT
/sbin/modprobe ipt_LOG


# 3. Flush & Reset iptables settings

/sbin/iptables -F
/sbin/iptables -X
/sbin/iptables -Z
/sbin/iptables -F -t nat
/sbin/iptables -X -t nat
/sbin/iptables -Z -t nat
/sbin/iptables -P INPUT DROP
/sbin/iptables -P OUTPUT ACCEPT
/sbin/iptables -P FORWARD ACCEPT
/sbin/iptables -t nat -P PREROUTING ACCEPT
/sbin/iptables -t nat -P POSTROUTING ACCEPT
/sbin/iptables -t nat -P OUTPUT ACCEPT


# 4. Accept connection from LOCALHOST ( loopback )

/sbin/iptables -A INPUT -i lo -j ACCEPT


# 5. Acception connection from some ports ( Firewall rules )

# /sbin/iptables -A INPUT -m state --state NEW -p tcp --dport 80 -j ACCEPT

# 6. Source NAT settings

/sbin/iptables -A INPUT -i $LAN_IF -j ACCEPT
echo "1" > /proc/sys/net/ipv4/ip_forward
/sbin/iptables -A POSTROUTING -t nat -o $WAN_IF -s $LAN_IP -j SNAT --to $WAN_IP

# 8. Accept connection from ESTABLISHED and RELATED connection

/sbin/iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

```

DNAT 就是這一段了
我有空研究再補
通常對方沒設定 SNAT 的時候
只能設定 DNAT(或安全因素)

```
/sbin/iptables -t nat -A PREROUTING -p tcp -i ppp0 --dport 80 -j DNAT --to 192.168.100.104:80
```

最後設定這個不如使用 VPN 實做會不會比較方便?
有時間研究

## 番外 iptables 可以設定對網域做調整

[Blocking HTTP requests via Iptables for a specific domain – Defragged](https://defragged.org/2020/03/14/blocking-http-requests-via-iptables-for-a-specific-domain/)
