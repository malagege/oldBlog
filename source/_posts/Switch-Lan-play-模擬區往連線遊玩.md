---
title: Switch Lan play 模擬區往連線遊玩
date: 2022-02-13 22:55:18
tags: [switch,lan,switch]
categories: 網路
---


官網詳細教學:[LAN Play Status | lan-play.com](http://www.lan-play.com/install-switch)

```bash
# 下載程式
wget https://github.com/spacemeowx2/switch-lan-play/releases/download/v0.2.3/lan-play-linux
# 執行權限
chmod +x lan-play-linux
# root 執行程式
sudo ./lan-play-linux --relay-server-addr switchlanplay-tw.ddns.net:11451 --netif  __你的網路卡__
```

<!--more-->


不知道是不是我朋友有防火牆(Fortigate)關係，他少一個 log。所以我們沒連線成功...

我的 Client
![](https://imgur.com/ht9dKbV.png)
我朋友的 Client
![](https://i.imgur.com/zaklbCL.png)


##  原理

詳細內容: [Switch lan-play原理 – 夜明的孤行燈](https://www.huangyunkun.com/2020/04/08/switch-lan-play/)[備份圖](https://i.imgur.com/hOduBFh.png)


我沒有想到可以用程式方式去做轉送封包，這個解法很有趣。感覺能運用在某些地方。不過這種方法必須2台當 Router。所以沒有說到實用。
從官網看到PS4也能做到。

## 嘗試區網研究資料(彩蛋)

本來想試試這個能不能簡單用區網連線，查看原理需要四台電腦，不過遊玩電腦會沒辦法連線上網，所以這種可能也不是很好遊玩體驗。因為我想試試連線功能，但是我家沒有 vlan 設備機器，想看看 VM 是不是可以做 vlan 動作，從 PVE 看到 KVM(libvirt)，因為不太想重灌，PVE設定 openSwitch 好像又是一道門檻，想直接模擬出來最好，有看到 iproute2 用 vlan ，在安裝ubuntu 時候有看到 mutlipass 這套工具，可以像 vagrant 快速建立好 KVM，不過 mutlipass networks 無法使用，好像目前沒有對此作設定，所以我透過GUI設定選到 bridge(vlan) ，不過用完電腦整台連線會有問題...，所以目前失敗。也許用實體swtich 測試實體機器會比較快，但是就是沒機器...，不過這次也讓我玩到 KVM 這一塊，單單啟動一台虛擬機器 multipass 不會輸給 vagrant， 快速建置 ubuntu 真的很方便。有時間我再來研究這個。

以下我爬到的文章，這邊就不整理了，隨便紀錄。

[socat 广播以及多播 - 荣锋亮 - 博客园](https://www.cnblogs.com/rongfengliang/p/9667003.html)

[Ubuntu 上 Vagrant+Libvirt 虛擬環境 - 我是山姆鍋](https://samkuo.me/post/2020/06/vagrant-libvirt-ubuntu-devops-env/)
[GitHub - aesirteam/openvswitch-demo](https://github.com/aesirteam/openvswitch-demo)
[手把手教你 Linux 是如何實現 VLAN 的 - 台部落](https://www.twblogs.net/a/5c99d887bd9eee42500804e5)
[Linux下vlan的学习（下） | 污力大熊的博客](http://zhongm.in/2017/01/17/Learning-Linux-Vlan-2/)

[How to configure VLAN network in Ubuntu - {With examples}](https://foofunc.com/how-to-configure-vlan-network-in-ubuntu/)


[networking - How do I configure a vlan-linked bridge in netplan to serve a VM? - Ask Ubuntu](https://askubuntu.com/questions/1305542/how-do-i-configure-a-vlan-linked-bridge-in-netplan-to-serve-a-vm)


[Create Bonding, Vlan, Bridge Interface in Ubuntu 18 Using Netplan and networkd | by Restu Nursobah | Medium](https://medium.com/@restu.nursobah/create-bonding-vlan-bridge-interface-in-ubuntu-18-using-netplan-and-networkd-a1189848c639)


[Multipass — 如 Docker 般的虛擬機. 好讀版： | by Jack Kuo | Medium](https://jackkuo-tw.medium.com/multipass-%E5%A6%82-docker-%E8%88%AC%E7%9A%84%E8%99%9B%E6%93%AC%E6%A9%9F-e19e3e36aec3)

```
multipass launch 16.04 -c 4 -m 4G -d 20G -n ubuntu 


service networking force-reload 

multipass launch 16.04 -c 1 -m 512M -d 2G -n kvm1 


multipass launch 16.04 -c 1 -m 256M -d 20G -n ubuntu --network name=brvlan1,mode=manual

##
auto wlp3s0.1
iface wlp3s0.1 inet manual
        vlan-raw-device wlp3s0

auto brvlan1
iface brvlan1 inet manual
        bridge_stp off
        bridge_waitport 0
        bridge_fd 0
        bridge_ports wlp3s0.1

auto wlp3s0.2
iface wlp3s0.2 inet manual
        vlan-raw-device wlp3s0

auto brvlan2
iface brvlan2 inet manual
        bridge_stp off
        bridge_waitport 0
        bridge_fd 0
        bridge_ports wlp3s0.2

##
sudo ip link add link wlp3s0 name wlp3s0.100 type vlan id 100
sudo ip link set dev wlp3s0.100 up
sudo ip link add link wlp3s0 name wlp3s0.200 type vlan id 200
sudo ip link set dev wlp3s0.100 master brvlan1
sudo ip link set dev wlp3s0.200 master brvlan2

sudo ip link set dev wlp3s0.200 up
sudo ip link add name brvlan1 type bridge
sudo ip link set dev brvlan1 up
sudo ip link add name brvlan2 type bridge
sudo ip link set dev brvlan2 up
```

[Additional network interfaces | Multipass documentation](https://multipass.run/docs/additional-networks)

[虚拟化技术之kvm管理工具virsh常用基础命令（一） - Linux-1874 - 博客园](https://www.cnblogs.com/qiuhom-1874/p/13508231.html)

[How to configure a VLAN in Linux | Enable Sysadmin](https://www.redhat.com/sysadmin/vlans-configuration)

[Linux Bridge 詳解_我在對面的角落 - MdEditor](https://www.gushiciku.cn/pl/pop3/zh-tw)
[Linux 虚拟网络设备 veth-pair 详解，看这一篇就够了 - 猿大白 - 博客园](https://www.cnblogs.com/bakari/p/10613710.html)

[Linux網路裝置veth pair和netns_我在對面的角落 - MdEditor](https://www.gushiciku.cn/pl/pnf1/zh-tw)

[云计算底层技术-虚拟网络设备(Bridge,VLAN) | opengers](https://opengers.github.io/openstack/openstack-base-virtual-network-devices-bridge-and-vlan/)

[Create VLAN bridge under Linux (trunk, IEEE 802.1Q) | panticz.de](http://www.panticz.de/linux-vlan-bridge)