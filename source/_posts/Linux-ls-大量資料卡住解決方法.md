---
title: Linux ls 大量檔案卡住解決方法
date: 2019-09-27 10:35:21
tags: [ls, linux]
categories: Linux
---

今天在大量檔案資料夾(約19萬個檔案)
下 command line 下 ls 之後就卡住
不能用 `Ctrl + C` 跳出去...

<!--more-->

在想有什麼方法解決這個問題?

## ls -1 -f

**注意這邊 -1 是數字1，不是英文小寫L**
查看了一下 `-f` 就是不做排序，`-1`是做一欄輸出

[Linux下文件过多过碎问题 – 工作笔记](http://www.zxzblog.com/linux-morefile/)

## 刪除多個檔案

雖然我沒有現在要做，不過都看到方法，順便記錄一下。

### strace

這篇用[非常大的文件夹导致ls命令卡住](https://beyondkmp.github.io/%E5%B8%B8%E7%94%A8%E6%8A%80%E5%B7%A7/ls%E5%91%BD%E4%BB%A4%E5%8D%A1%E4%BD%8F.html) strace 方法

```bash
ls -l >/dev/null
strace -p 3963 2>&1 | grep lstat

# lstat64(“xxxxx”, {st_mode=S_IFREG|0664, st_size=0, …}) = 0
```

[Linux 删除海量文件 rm -f 会卡死怎么破？ - OSCHINA](https://www.oschina.net/question/255612_220479)

### rsync --delete-before

```bash
rsync –delete-before -d -a –progress –stats /a/ /data/
```
[Linux下文件过多过碎问题 – 工作笔记](http://www.zxzblog.com/linux-morefile/)
[还是Linux删除海量文件的问题，那些说用rsync --delete-before完全没用 - OSCHINA](https://www.oschina.net/question/255612_221510)


### 別人多個測試結果

> http://www.slashroot.in/which-is-the-fastest-method-to-delete-files-in-linux
> 刪除50萬個文件,各種方法的速度對比:
> 
> 先創建50萬個文件:
> for i in $(seq 1 500000); do echo testing >> $i.txt; done
> 
> 各種刪除當前目錄文件方法,先進入該目錄(比如PHP的sessions目錄),然後執行以下命令:
> time rm -f * 刪除失敗,返回 /bin/rm: Argument list too long
> time find ./ -type f -exec rm {} \;
> time find ./ -type f -delete
> time rsync -a --delete EmptyDir/ BigDir/
> time perl -e 'for(<*>){((stat)9<(unlink))}'
> 
> 結果:
> perl用了1分鐘
> rsync用了3分鐘
> find with -delete用了5分鐘
> find with -exec用了14分鐘
> 直接用rm結果是刪除失敗. 

### 實測

```bash
# 建立 500000 測試文件
for i in $(seq 1 500000); do echo testing >> $i.txt; done

find /test -type f -exec rm {} \;
find /test -mtime +7 -exec rm {} \;
find /test -size +7M -exec rm {} \;

time rm -f * #/bin/rm: Argument list too long
time find ./ -type f -exec rm {} \;      #real    14m51.735s
time find ./ -type f -delete     # real    5m11.937s
time perl -e 'for(<*>){((stat)[9]<(unlink))}'   # real    1m0.488s (王者)
time rsync -a --delete blanktest/ test/   # real    2m52.502s
```

測試 Perl 真的很快，不知道是用甚麼巫術。

推薦詳細可以看[Rsync同步时删除多余文件 [附：删除大量文件方法的效率对比] - 散尽浮华 - 博客园](https://www.cnblogs.com/kevingrace/p/5766139.html) {% asset_link web1.png 備份圖 %}
裡面介紹很詳細

```bash
# #1 刪除某資料夾檔案
#1）建立一個空的文件夾：
# mkdir /tmp/test
#2）用rsync刪除目標目錄：
# rsync --delete-before -a -H -v --progress --stats /tmp/test/ log/
這樣要刪除的log目錄就會被清空了，刪除的速度會非常快。rsync實際上用的是替換原理，處理數十萬個文件也是秒刪。

# #2 刪除某資料夾檔案
# 搬移後刪除
rsync -a --remove-source-files SOURCEDIR /mnt/TARGETDIR/
```

[Linux 刪除海量文件 rm -f 會卡死怎麼破？ - OSCHINA](https://www.oschina.net/question/255612_220479)

## 大量檔案複製

> 3、小文件太多，複製太慢
> 　　1，在需要對大量小文件進行移動或複製時，用cp、mv都會顯得很沒有效率，可以用tar先壓縮再解壓縮的方式。
> 　　2，在網絡環境中傳輸時，可以再結合nc命令，通過管道和tcp端口進行傳輸。
> 　　nc和tar可以用來快速的在兩台機器之間傳輸文件和目錄，比ftp和scp要來得簡單的多。
> 　　由於nc是一個超輕量的命令，所以一般busybox都會集成它。當一個linux終端，比如linux pda,
> 　　通過usblan的方式連接到另一台linux主機的時候，這樣的嵌入式終端上一般不會集成ftp server, ssh server
> 　　這樣比較笨重的服務，這個時候， nc可能成為唯一的上傳手段。
> 　　比如將機器A上的test目錄上傳到到機器 B（192.168.0.11）上，只需要：
> 　　在機器B上，用nc來監聽一個端口，隨便就好，只要不被佔用；並且將收到的數據用tar展開。-l代表監聽模式。
> 　　#nc -l 4444 |tar -C /tmp/dir -zxf -
> 　　然後，在A上通過nc和 tar發送test目錄。使用一致的4444的端口。
> 　　#tar -zcvf - test|nc 192.168.0.11 4444

[Linux下文件過多過碎問題 – 工作筆記](http://www.zxzblog.com/linux-morefile/)

~~哪天用到再說...~~

### 實測

最近工作有座大量刪除檔案，在我還滿好奇使用這個速度會有多快?上面指令竟然無法使用!!!不過後來有找到類似指令，最後測試成功。
參考: [Netcat（Linux nc 指令）網路管理者工具實用範例| 兩台主機之間複製整個目錄 - G. T. Wang](https://blog.gtwang.org/linux/linux-utility-netcat-examples/)

下面操作要照順序

在接收端電腦使用

```bash
nc -l 5000 | tar xvf -
```

在發送端電腦使用

```bash
# localhost 換成你的目地電腦
# 前面可以加 time ，可以算時間
# /path/to/dir 是要傳送檔案目錄
# 5000 傳輸 port
tar cvf - /path/to/dir | nc localhost 5000

```

相同電腦，執行速度

```
real    4m43.463s
user    0m9.270s
sys     0m47.256s
```

### zxvf 操作

不會比較快，而且還會卡住...，但其實是完成的。




## 大量看檔案數

```bash
time perl -e 'opendir D, "."; @files = readdir D; closedir D; print scalar(@files)."\n"' #1315029  real    0m0.580s user    0m0.302s sys     0m0.275s – 
```

我這邊測試時間
```
real    0m1.620s
user    0m0.101s
sys     0m0.565s
```

使用`time ls -f | wc -l`時間好像更快

```
real    0m0.397s
user    0m0.175s
sys     0m0.250s
```

參考:
- [shell - Fast Linux file count for a large number of files - Stack Overflow](https://stackoverflow.com/questions/1427032/fast-linux-file-count-for-a-large-number-of-files)
- [PERL count files in a dir](https://www.unix.com/shell-programming-and-scripting/113891-perl-count-files-dir.html)

## 找大量使用inode 資料夾

```bash
wget http://downinfo.myhostadmin.net/check_inode_counts.txt
mv check_inode_counts.txt check_inode_counts.pl
perl check_inode_counts.pl /
```

check_inode_counts.pl
```perl
#!/usr/bin/perl -w
use strict;
my $threshold=50000;
my ($count,$bigdir);
sub count_inodes($);
sub count_inodes($)
{
	my $dir=shift;
	return if($count>=$threshold);
	if(opendir(my $dh,$dir))
	{
		while(defined(my $file=readdir($dh)))
		{
			next if ($file eq '.'||$file eq '..');
			$count++;
			my $path=$dir.'/'.$file;
			count_inodes($path) if (-d $path);
		}
		closedir($dh);
	}else
	{
		warn "couldn't open $dir - $!\n";
	}
}

push(@ARGV, '.') unless (@ARGV);
while (@ARGV)
{
	my $main=shift;
	$main.='/' unless($main=~/\/$/);
	if(opendir(my $dh, $main))
	{
		while(defined(my $file=readdir($dh)))
		{
			$count=0;
			next if($file eq '.'||$file eq '..'||! -d $main.$file);
			count_inodes($main.$file);
			$count=">".$threshold if($count>=$threshold);
			printf "%10s\t%s\n", $count, $main.$file;
		}
		closedir($dh);
	}
	else
	{
		warn "couldn't open $main - $!\n";
	}
}

```

[linux系统磁盘block、inode占满处理 - 华为云](https://www.huaweicloud.com/articles/b989923796f37fb1736e0063f83b2c24.html)

## 大量檔案看大小


[command line - Fast way to display the size of each subdirectory in a directory - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/307583/fast-way-to-display-the-size-of-each-subdirectory-in-a-directory)

[One billion files on Linux [LWN.net]](https://lwn.net/Articles/400629/)

[linux - Filesystem large number of files in a single directory - Server Fault](https://serverfault.com/questions/43133/filesystem-large-number-of-files-in-a-single-directory)

[XFS vs EXT4 | 夏天的风的博客](http://xiaqunfeng.cc/2017/07/06/XFS-vs-EXT4/)


