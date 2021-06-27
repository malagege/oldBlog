---
title: Linux 執行多個目錄全部指令方法
date: 2020-09-11 13:09:33
tags: [linux, shell]
categories: Linux
---

最近抓取一些專案
需要執行 mvn install
但是非常多專案
在想有什麼快速方法(雖然已經手動執行完了)

<!--more-->

最近看到[Linux 執行目錄下所有 Script](https://www.opencli.com/linux/linux-run-all-scripts-in-directory)可以解決我的需求
就嘗試看看

## run-parts

run-parts 就是執行當下所有目錄的程式，這之前在 crontab 有看到
但沒有仔細去研究

```sh
run-parts –regex '.*sh$' /home/pi/shell/
```

### 預設抓取檔案

```
run-parts  --list  /home/malagege/run-parts
/home/malagege/run-parts/test
```
所以記得要寫 regex

### 相關文章

- [如何將目錄下的腳本一次性全部執行 - 良許Linux - 博客園](https://www.cnblogs.com/yychuyu/p/12983328.html)
- [Linux之 run-parts 命令的用法及原理_一次次尝试-CSDN博客](https://blog.csdn.net/WYpersist/article/details/80246860)
- [run-parts/run-parts at master · ikysil/run-parts](https://github.com/ikysil/run-parts/blob/master/run-parts)
- [請問 "run-parts" 這指令](http://phorum.study-area.org/index.php?topic=16017.0)
這邊有提到順序
> 以 file name 字母順序來排：
> ls /path/to/dir | sort
- [Fix "Exec format error" When Running Scripts With run-parts Command](https://ostechnix.com/fix-exec-format-error-when-running-scripts-with-run-parts-command/)
> #!/usr/bin/python3
> We can use:
> #!/usr/bin/env python3


## find

這個可能適合用在我的需求

```bash
find /home/pi/shell/ -maxdepth 1 -type f -executable -name '*.sh' -exec {} \;
```

改寫

 -executable 需要拿掉，這個是抓可執行的檔案

```bash
find . -maxdepth 2 -type f -executable -name 'composer.json' -exec composer update  \;
```

卻不能跑

後面改成 -exec pwd \;
發現路徑是第一層路徑

### 使用 execdir

```bash
find . -maxdepth 2 -type f  -name 'composer.json' -execdir  composer update  \;
```

[应该在find命令中使用-execdir代替-exec_一程山水一程歌-CSDN博客](https://blog.csdn.net/Qidi_Huang/article/details/53388589)


### 搬檔案可參考

```
rsync -a --remove-source-files SOURCEDIR /mnt/TARGETDIR/
```

[如何在 Linux 搬動資料夾與其所有檔案到另一個同名資料夾 | The Will Will Web](https://blog.miniasp.com/post/2021/06/25/Move-folder-and-files-to-same-folder-name-on-another-disk?fbclid=IwAR33EHmCeKLFk8CC1Tlr-i2gsWEb-WZP1RGH2wVHNMoWPJT7PNjFvKtei_c)
