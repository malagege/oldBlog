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
> 　　#nc -l 4444 |tar -C /tmp/dir -zxf –
> 　　然後，在A上通過nc和 tar發送test目錄。使用一致的4444的端口。
> 　　#tar -zcvf – test|nc 192.168.0.11 4444

[Linux下文件過多過碎問題 – 工作筆記](http://www.zxzblog.com/linux-morefile/)

哪天用到再說...