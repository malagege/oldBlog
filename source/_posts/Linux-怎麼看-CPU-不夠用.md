---
title: Linux 怎麼看 CPU 不夠用
date: 2020-09-24 22:40:11
tags: [linux, cpu]
categories: Linux
---

最近看到[認識CPU | 沉迷思考的魚](http://ypk1226.com/2019/04/17/java/cpu-usage/)
就好奇怎麼看CPU不夠力
最後來整理一下

<!--more-->

相關 CPU文章

1. [認識CPU | 沉迷思考的魚](http://ypk1226.com/2019/04/17/java/cpu-usage/)
2. [理解Linux系統負荷 - 阮一峰的網絡日誌](https://www.ruanyifeng.com/blog/2011/07/linux_load_average_explained.html)
3. [CPU的load和使用率傻傻分不清 - 昀溪 - 博客園](https://www.cnblogs.com/rexcheny/p/9382396.html)

## 查詢 CPU 核心和線程

Linux 
```bash
# 查看 cpu 型號
sudo dmidecode -s processor-version
# 查看 cpu 個數
grep 'physical id' /proc/cpuinfo | sort -u | wc -l
# 查看核心數
grep 'core id' /proc/cpuinfo | sort -u | wc -l
# 查看線程數
grep 'processor' /proc/cpuinfo | sort -u | wc -l
```

Mac
```bash
# 查看核心數
sysctl hw.physicalcpu
# 查看線程數
sysctl hw.logicalcpu
```

## 系統負荷怎麼看

系統負荷簡單就能看 CPU 能不能夠用

```bash
top
uptime
```
都可以看到 `load average: 0.09, 0.04, 0.00`
這邊注意有三個數字，相當 5分鐘, 10 分鐘 ,15 分鐘系統負載
我們通常注意最後一個數字(15分鐘的)
怎樣數字能判斷?

> 系統負荷的經驗法則
> 
> 1.0是系統負荷的理想值嗎？
> 不一定，系統管理員往往會留一點餘地，當這個值達到0.7，就應當引起注意了。經驗法則是這樣的：
> 當系統負荷持續大於0.7，你必須開始調查了，問題出在哪裡，防止情況惡化。
> 當系統負荷持續大於1.0，你必須動手尋找解決辦法，把這個值降下來。
> 當系統負荷達到5.0，就表明你的系統有很嚴重的問題，長時間沒有響應，或者接近死機了。你不應該讓系統達到這個值。
參考: [認識CPU | 沉迷思考的魚](http://ypk1226.com/2019/04/17/java/cpu-usage/)

這邊要注意，這邊 1 全部是單指單核心、單一進程
下面會講一下多線程

## 多核心怎麼看系統負荷夠用?

> 總核數 = 物理CPU個數 * 每個物理CPU的核心數
> 總邏輯CPU數 = 物理CPU個數 * 每個物理CPU的核心數 * 超線程數量
參考[CPU的load和使用率傻傻分不清 - 昀溪 - 博客園](https://www.cnblogs.com/rexcheny/p/9382396.html)

簡單來看把最上面三個指令執行一下相乘後，就可以看到系統負載最大值

```
# 查看 cpu 個數
grep 'physical id' /proc/cpuinfo | sort -u | wc -l
2
# 查看核心數
grep 'core id' /proc/cpuinfo | sort -u | wc -l
4
# 查看線程數
grep 'processor' /proc/cpuinfo | sort -u | wc -l
6
```

這邊相乘就是 48

PS: 所以在多核心 Linux 下的 CPU 使用率超過 100% 是正常的

## 實際觀察

我目前某一個排程主機 Jenkins 上板會把所有排程打開，這樣會有一個嚴重問題，當下記憶體、CPU 過重
需要人工去開啟未成功排程
最近看了這篇文章
top 指令當下是

{% asset_img img1.jpg 系統負載 %}

這邊也有看到文章說 5分鐘系統負載持續飆高 後面 10,15 分鐘的系統將會持續飆高
所以系統負載會不會好轉，可以從這邊看出來

然後在系統負載小於 48 時候，我在 2x 開啟排程都能順利執行


## 番外篇 記憶體怎麼看

之前我在玩樹梅派開很久會發現我 RAM 怎麼沒有降下去
後來看鳥哥文章看到 Linux RAM 不會馬上釋放掉
之後有看到文章說 swap 吃太多的時候，就要考慮到是不是記憶體不足?
有想到再補

[手動清除 Linux 記憶體快取. /proc/sys/vm/drop_caches | by Yu-Hsin Hung | Hungys.blog() | Medium](https://medium.com/hungys-blog/clear-linux-memory-cache-manually-90bec95ea003)
[尋找當下 Linux OS 效能瓶頸 - Phil Workspace](https://blog.pichuang.com.tw/20200307-performance-collection/)