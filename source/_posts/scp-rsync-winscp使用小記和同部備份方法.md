---
title: "scp,rsync,winscp使用小記和同部備份方法"
date: 2019-03-23 01:28:03
tags: [scp, rsync, winscp]
categories: Linux
---

最近想同步檔案道別台機器
想比較 scp,rsync,winscp 有什麼同步的方法
比較一下差異

<!--more-->

## scp

之前有寫過這篇[scp 跟 sftp 是不一樣的東西 | 程式狂想筆記](https://malagege.github.io/blog/2018/04/16/scp%E8%B7%9Fsftp%E6%98%AF%E4%B8%8D%E6%A8%A3%E7%9A%84%E6%9D%B1%E8%A5%BF/)
所以這個沒有辦法跟 rysnc 一樣判斷同步
但有加密功能
使用也很簡單，不需要先 ssh 別台主機
直接 command 使用`scp xxx@ooo:xxx fff@bbb:xxx`
不過路徑可能要清楚是哪邊

複製資料夾除了用\*方式
但用星號無法多層
要使用`-r` Recursively copy entire directories
`scp -r user@your.server.example.com:/path/to/foo /home/user/Desktop/`
[shell - How to copy a folder from remote to local using scp? - Stack Overflow](https://stackoverflow.com/questions/11304895/how-to-copy-a-folder-from-remote-to-local-using-scp)

## rsync

~~跟 scp 差別預設沒有加密，但還是有辦法做加密。~~
剛看了一下別的文章
2004 年後預設說有做 ssh 加密
難怪我有看到有些說預設有 ssh 加密，有些沒有...
可參考[Rsync -e option to ssh - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/312358/rsync-e-option-to-ssh)

> Assuming you're not needing to consider the rsync protocol itself, this depends on the compilation options used for your version of rsync.
> It used to be the case that rsync used the rsh protocol by default (unless the build-time default had been changed), but the build-time default changed to ssh in 2004.
> If you're in doubt, start the rsync process transferring a relatively large file (or collection of files), and in another terminal run ps -ef | grep [r]sync. This will show you whether or not you're using ssh for a transport. Here's an example from one of my servers, which clearly shows the ssh transport:
>
> ```
> root     28057 27173  1 09:48 pts/4    00:00:00 rsync -avHP --dry-run /home roaima@otherserver:
> root     28058 28057  0 09:48 pts/4    00:00:00 ssh -l roaima otherserver rsync --server -vnlHogDtpre.iLsfx --partial . .
> ```
>
> Finally, be aware that the compilation default can be overridden with the RSYNC_RSH environment variable. (See the man page for further details.)

有時間在驗證上面到底有沒有加密

他有很多參數
目前我常用是用`-av`，不過是先前前輩給我用的設定
我還沒搞清楚

```

-a, --archive 歸檔模式，表示以遞歸方式傳輸文件，並保持所有文件屬性，等於-rlptgoD
-v, --verbose 詳細模式輸出
--delete 刪除那些目標目錄中有而源目錄中沒有的多餘文件。這個是 rsync 做增量方式的全備份的最佳選擇方案！！！！！！
簡單說加入`--delete`就是本基做刪除動作，但是遠端就會**做刪除**
反之，沒家的話就不會。

```

詳細記紹可以看
[SCP 和 Rsync 遠程拷貝的幾個技巧 - 散盡浮華 - 博客園](https://www.cnblogs.com/kevingrace/p/8529792.html) {% asset_link 1.png 備份圖 %}
[SSH,SCP,rsync,sftp - 简书](https://www.jianshu.com/p/52aa5c2b872a) {% asset_link 2.png 備份圖 %}

不過 window 就算有裝 git bash，但預設沒有 rsync 可以用
所以我才會找找有沒有類似在 window winscp 做同步更新方法
當然我也有找到 window 能用 rsync
[Chocolatey Gallery | cwRsync (Free Edition) 5.5.0.20190204](https://chocolatey.org/packages/rsync)
[Sync-Rsync - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vscode-ext.sync-rsync&ssr=false#overview)

**2020-04-23**
```bash
rsync –av /etc server1:/tmp # 複製目錄和目錄下文件
rsync –av /etc/ server1:/tmp # 只複製目錄下文件
rsync -avh myfile.gz /home/pi/tmp/ # 複製檔案到資料夾
rsync -avzh pi@192.168.1.12:/mypath/myfile.gz /mybackup/
rsync -avzh --bwlimit=100K pi@192.168.1.12:/mypath/myfile.gz /mybackup/ # --bwlimit 限制頻寬
```

> -n 模擬複製過程
> -v 顯示詳細過程
> **-r 遞歸複製目錄樹**
> **-p 保留權限**
> **-t 保留時間戳**
> **-g 保留組信息**
> **-o 保留所有者信息**
> **-l 將軟鏈接文件本身進行複製（默認）**
> -L 將軟鏈接文件指向的文件複製
> -a 存檔，相當於–rlptgoD，但不保留ACL（-A）和SELinux屬性（-X）
> --delete  如果沒有來源檔案只有新增、沒有減少的話，它就跟一般的複製
> -h：將數字以比較容易閱讀的格式輸出。


## WinSCP

內建也有做同步功能
[Scripting and Task Automation :: WinSCP](https://winscp.net/eng/docs/scripting)
[Synchronize Dialog :: WinSCP](https://winscp.net/eng/docs/ui_synchronize)

其他參考來源:
[SCP 和 Rsync 远程拷贝的几个技巧 - 散尽浮华 - 博客园](https://www.cnblogs.com/kevingrace/p/8529792.html)
[使用 WinSCP 进行简单代码文件同步 - leestar54 - 博客园](https://www.cnblogs.com/leestar54/p/6625235.html#wiz_toc_11)
[Synchronize Dialog :: WinSCP](https://winscp.net/eng/docs/ui_synchronize)
[Scripting and Task Automation :: WinSCP](https://winscp.net/eng/docs/scripting)
[厭世人生的 MIS 記事本: rsync 參數 用法](https://mga8326.blogspot.com/2018/08/rsync.html)
[SSH,SCP,rsync,sftp - 简书](https://www.jianshu.com/p/52aa5c2b872a)
[backup - Do you need -e ssh for rsync? - Server Fault](https://serverfault.com/questions/378939/do-you-need-e-ssh-for-rsync)
[詳全文\_資料備份同步工具簡介— rsync](http://newsletter.ascc.sinica.edu.tw/news/read_news.php?nid=1742)
[rsync 備份 | Tsung's Blog](https://blog.longwin.com.tw/2005/12/rsync_ssh_backup/)
[Rsync 備份本地及遠端目錄](https://www.opencli.com/linux/rsync-local-remote-backup)

[How to Remove User and Hostname in Terminal Prompt - Make Tech Easier](https://www.maketecheasier.com/remove-user-hostname-terminal-prompt/)

date: 2019-03-31

最近在 window 使用 rsync
結果發現他還是全部檔案會傳上去
Google 一下，發現要加`--modify-window=1`~~才能解決全部上傳問題~~我自己電腦測試失敗...
但發現可能是我在 linux 做`chmod o+rx -R .`關係，因為剛開始複製是`750`
晚點看看`--chmod=755`能否解決?

但原始資料來源，做 rsync 好像正常...
有看到用`--size-only`但這也不是最好的同步方式
難免改檔案大小都一樣

- [–checksum 和–ignore-times 选项之间的 Rsync 差异 - 代码日志](https://codeday.me/bug/20181107/355365.html)
- [将使用 rsync 增量的文件备份到 NTFS 分区 Backup files incremental with rsync to a NTFS-Partition-rootopen.com](https://www.rootopen.com/command/view/5b28ae76d678fd520c7275dc)
- [Linux 命令：Rsync 常见问题之 Debug | Jason's Blog](http://chengkinhung.blogspot.com/2012/09/linuxrsyncdebug.html)
- [如何在 FAT 文件系统中使用 rsync？ | webmotociclismo.com](http://webmotociclismo.com/questions/222/ru-he-zai-fatwen-jian-xi-tong-zhong-shi-yong-rsync)
- [一千萬個為什麽 | 僅在從 ext4 同步到 NTFS 時顯示已更改的文件](https://www.weiyeying.com/ask/4130673)
- [chmod rsync - Google 搜尋](https://www.google.com/search?client=firefox-b-d&ei=GICgXIeLPMj-8gXliLSIDg&q=chmod+rsync&oq=chmod+rsync&gs_l=psy-ab.3..0i203j0i8i30l9.1489.5065..5199...1.0..0.70.595.11......0....1..gws-wiz.......0i71j0i67j0j0i13j0i30.50ZRAKl16jw)
- [就是愛點鬧: Windows 下好用的檔案同步工具](http://idannow.blogspot.com/2013/03/windows.html)
- [【 Linux 】檔案同步工具 rsync 指令 - 亞索數位筆記](https://blog.aiso.idv.tw/
- [介绍 rsync 同步工具配置与使用，linuxlinux, linuxwindows(cwRsync) - lsbwahaha - BlogJava](http://www.blogjava.net/lsbwahaha/archive/2010/02/03/311812.html)
- [rsync 使用简介 | 喵 ♂ 呜 的博客](https://blog.yumc.pw/posts/Rsync-Usage/)
- [rsync：--size-only 和--ignore-times 之间的区别 | landcareweb.com](http://landcareweb.com/questions/3793/rsync-size-onlyhe-ignore-timeszhi-jian-de-qu-bie)
- [rsync：–size-only 和–ignore-times 之间的差异 - 代码日志](https://codeday.me/bug/20170724/45245.html)
- [–checksum 和–ignore-times 选项之间的 Rsync 差异 - 代码日志](https://codeday.me/bug/20181107/355365.html)
- [「」checksum 和」ignore-times 選項之間的 Rsync 差異](https://question.itread01.com/fipeyp.html)
- [–checksum 和–ignore-times 选项之间的 Rsync 差异 - 代码日志](https://codeday.me/bug/20181107/355365.html)

目前我有一隻 window->linux

```bash
rsync -vr --progress --chmod=755 ./local_path/ user@ip:/path
```

參考來源：

- [将使用 rsync 增量的文件备份到 NTFS 分区 Backup files incremental with rsync to a NTFS-Partition-rootopen.com](https://www.rootopen.com/command/view/5b28ae76d678fd520c7275dc)
- [Linux 命令：Rsync 常见问题之 Debug | Jason's Blog](http://chengkinhung.blogspot.com/2012/09/linuxrsyncdebug.html)
- [如何在 FAT 文件系统中使用 rsync？ | webmotociclismo.com](http://webmotociclismo.com/questions/222/ru-he-zai-fatwen-jian-xi-tong-zhong-shi-yong-rsync)
