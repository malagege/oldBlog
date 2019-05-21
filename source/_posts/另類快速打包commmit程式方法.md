---
title: 另類快速打包commmit程式方法
date: 2019-05-20 23:35:45
tags: [shell]
categories: shell
---

[快速打包程式的腳本(shell) | 程式狂想筆記](https://malagege.github.io/blog/2019/01/05/%E5%BF%AB%E9%80%9F%E6%89%93%E5%8C%85%E7%A8%8B%E5%BC%8F%E7%9A%84%E8%85%B3%E6%9C%AC-shell/)
之前有寫過這篇，再包程式都是固定的程式
有時候公司需要打包只有異動到的檔案
這時候之前寫的程式內容可以修正
但我在想有什麼快速整理

<!--more-->

## 使用 GIT 抓取異動檔案

`git diff --stat`
會顯示異動檔案，當然也會有後面的異動行數

```bash
git diff head^ --name-only
```

可抓上次異動的檔案

這時候可以貼到[快速打包程式的腳本(shell) | 程式狂想筆記](https://malagege.github.io/blog/2019/01/05/%E5%BF%AB%E9%80%9F%E6%89%93%E5%8C%85%E7%A8%8B%E5%BC%8F%E7%9A%84%E8%85%B3%E6%9C%AC-shell/)之前的 code

## cp 複製檔案

假如複製少許檔案
使用 git diff 抓取少許檔案
為了貼一個檔案，就用腳本工具也挺麻煩
可以直接使用`cp`指令

在[bash - Linux: copy and create destination dir if it does not exist - Stack Overflow](https://stackoverflow.com/a/8722721)有提到用`--parents`可以解決問題

```bash
/tmp $ mkdir foo
/tmp $ mkdir foo/foo
/tmp $ touch foo/foo/foo.txt
/tmp $ mkdir bar
/tmp $ cp --parents foo/foo/foo.txt bar
/tmp $ ls bar/foo/foo
foo.txt
```

複製檔案範例

```
cp --parents foo/foo/foo.txt(打包檔案) ~/(打包路徑)
#ex
cp --parent ooo/xxx.php ~/Desktop
```

用 Sublime Text 快速貼上也很快 XD

### cp 另類複製方法

但有看到 Mac 不能用`--parents`
有看到一種方法

```bash
mkdir -p /foo/bar && cp myfile.txt $_
```

![參考網址](https://stackoverflow.com/a/32596855)

查了一下，`$_`這個參數很特別
是抓取前一個指令最後面一個參數

```bash
git
echo $_
# git
ll
echo $)
#-l  (抓取上一個指令最後一個參數，若沒參數就是指令)
```

所以\$\_可以做這樣運用

[Bash shell script 教學](https://www.slideshare.net/misscoming/bash-shell-script)
[bash-shell-script-10-638.jpg（JPEG 圖片，638x479 像素）](https://image.slidesharecdn.com/bashshellscript-150218092059-conversion-gate01/95/bash-shell-script-10-638.jpg?cb=1424287937)
