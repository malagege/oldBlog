---
title: XFS 程式出現 no space left on device
date: 2021-06-14 14:33:04
tags: [linux,xfs,storage]
categories: Linux
---

通常都知道使用空間滿了就放不了檔案
但是很常漏掉 inode 滿了也放不了
[系統上檔案數量限制 | 程式狂想筆記](https://malagege.github.io/blog/2021/06/02/%E7%B3%BB%E7%B5%B1%E4%B8%8A%E6%AA%94%E6%A1%88%E6%95%B8%E9%87%8F%E9%99%90%E5%88%B6/)
這篇有特別紀錄
最近我專案碰的機器有時候會遇到程式存檔案到某資料(裡面有很多小資料)

<!--more-->


最近爬到 1TB 會遇到 no space left on device
**但這邊對回我目前使用 300GB 空間應該不太可能**
不過這邊跟 SE 提交一下這個
看能不能解決這個問題


簡單來說，預設掛 XFS 都是以 inode32
選用 inode64 就比較不會遇到問題

## 解決方案文獻

* [請教 XFS， JFS， UFS， EXT4， reiser 幾個檔案系統的問題 - 摸鱼](https://www.mofish.work/thread/13977



* [linux - XFS No space left on device - Stack Overflow](https://stackoverflow.com/questions/26036190/xfs-no-space-left-on-device)

指細看一下這篇，好像這篇也不到 1TB

> There is a bug with xfs_growfs which causes inodes to not be properly distributed across a partition. The solution is to simply remount with the inode64 option. For example, if this was the /dev/vda1, you would do the following:

```bash=
mount -o remount,inode64 /dev/vda1
```

> You can find more information about the bug at the following link:

http://xfs.org/index.php/XFS_FAQ#Q:_Why_do_I_receive_No_space_left_on_device_after_xfs_growfs.3F

* [Linux XFS: "no space left on device"_weixin_33737774的博客-CSDN博客](https://blog.csdn.net/weixin_33737774/article/details/85073460)

```
[root@rhn ~]# mount -o remount,inode64 /var/satellite
[root@rhn ~]# touch /var/satellite/a
[root@rhn ~]# rm -rf /var/satellite/a
```

* [No space left on device on xfs filesystem with 2.4T _冰冻vs西瓜的技术博客_51CTO博客](https://blog.51cto.com/molewan/1703109)

> 查询了下XFS FAQ，发现有一段：
>
> Q: What is the inode64 mount option for?
>
> By default, with 32bit inodes, XFS places inodes only in the first 1TB of a disk. If you have a disk with 100TB, all inodes will be stuck in the first TB. This can lead to strange things like "disk full" when you still have plenty space free, but there's no more place in the first TB to create a new inode. Also, performance sucks.
>
> To come around this, use the inode64 mount options for filesystems >1TB. Inodes will then be placed in the location where their data is, minimizing disk seeks.
> 
> Beware that some old programs might have problems reading 64bit inodes, especially over NFS. Your editor used inode64 for over a year with recent (openSUSE 11.1 and higher) distributions using NFS and Samba without any corruptions, so that might be a recent enough distro.
> 
> 
> 大意就是xfs文件系统会把inode存储在磁盘最开始的这1T空间里，如果这部分空间被完全填满了，那么就会出现磁盘空间不足的错误提示了。
> 
> 查看mount的man帮助手册，可以看到如下内容：

* [XFS文件系统报No space left on device 如何解决？ - 问答 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/ask/23438)

>    嘗試重新掛載：`mount -o remount -o noatime,nodiratime,inode64,nobarrier /dev/vdc1 /data`
> ext3文件系統使用inode table存儲inode信息，而xfs文件系統使用B+ tree存儲。考慮到性能問題，默認情況下這個B+ tree只會使用前1TB空間，當這1TB空間被寫滿後，就會導致無法寫入inode信息，報No spece left on device錯誤。在mount時，指定inode64即可將這個B+ tree使用的空間擴展到整個文件系統。


* [Linux超实用逻辑卷缩容与扩容亲自实操！ - 邓红 - 博客园](https://www.cnblogs.com/DanHog/p/13457291.html)
* [解决XFS文件系统NFS输出Stale NFS file handle错误 – 月与灯依旧](https://www.zhukun.net/archives/8025)

> 解決辦法：
1，掛載XFS分區時，記得添加inode64參數。否則會默認使用inode32來掛載。
2，本地目錄通過NFS export時，記得添加fsid=XX參數。

這篇還比較特別，提除另外一個第二解，但我不知道有沒有用。

[備份圖](https://i.imgur.com/ABk902m.png)


## 不錯文章

逛到不錯文章，特別留紀錄
留紀錄

* [目录中文件过多导致ls命令卡住 - 简书](https://www.jianshu.com/p/353a5dbcd423)

> 遍歷EXT3/4文件系統花這麼長時間和這麼多資源有時被認為是文件系統Bug。但是我個人認為，如果有Bug的話，那只能是遍歷軟件的Bug（比如，find命令、不帶-1和-f標識的ls命令以及各種備份軟件），而不是文件系統的Bug。

作者：Maslino
链接：https://www.jianshu.com/p/353a5dbcd423
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

這邊為什麼會看到很多人用 find 去解決 ls 問題


* [Linux 支援的檔案系統小評測-liuake-ChinaUnix博客](http://m.blog.chinaunix.net/uid-573799-id-2091461.html)

* [关于XFS文件系统的一个问题](https://groups.google.com/g/zh-kernel/c/7qlx4az_yDg)