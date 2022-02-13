---
title: Git diff  big5 亂碼解決方法
date: 2022-02-13 23:27:42
tags: [git,亂碼]
categories: Git
---

GitKaran 查看都是亂碼，後來找到解決方法。但VSCODE看程式碼納編會有亂碼問題。

<!--more-->

## 操作步驟

使用前怕Git改壞掉，建議可以先做zip備份。

[CHG: git fork diff 比較 的時候中文亂碼](https://charlottehong.blogspot.com/2021/08/git-fork-diff.html) [備份圖](https://i.imgur.com/dedGr2I.png)
[Git：Diff不处理除UTF-8以外的字符编码？ 中国服务器网](https://zgserver.com/gitdiffutf-8.html)
[gitattributes - How to create a Git attributes file - Stack Overflow](https://stackoverflow.com/questions/18331048/how-to-create-a-git-attributes-file)
[Git 屬性 - - Pro Git 繁體中文版](http://iissnan.com/progit/html/zh-tw/ch7_2.html)

`catdoc` 感覺很有趣，有空研究。

[.gitattributes 作用详细讲解（git大佬必会技能)_StarJava_的博客-CSDN博客_gitattributes](https://blog.csdn.net/qq_35425070/article/details/106883833)


### Step 1 gitconfig

```
# fork的git
%USERPROFILE%\AppData\Local\Fork\gitInstance\環境最新版\etc 
```

但我不想做全域，所以把在專案下`.git`做設定。

gitconfig
```
[diff "cp950"]
  textconv=iconv -f cp950 -t utf-8
```


### Step2 .gitattributes



[.gitattributes 作用详细讲解（git大佬必会技能)_StarJava_的博客-CSDN博客_gitattributes](https://blog.csdn.net/qq_35425070/article/details/106883833)

> .gitattributes生效順序
> 在一個Git庫中可以有多個.gitattributes 文件，不同.gitattributes 文件中，屬性設置的優先級(從高到低)如下：
> 
> /myproj/info/attributes 文件
> /myproj/my_path/.gitattributes 文件
> /myproj/.gitattributes 文件
> 同一個.gitattributes 文件中，遵循覆蓋原則，即後面的行會覆蓋前面的設置，如果一個文件的某個屬性被多次設置，則後設置的優先，類似 int a = 1; a = 2; 最終結果a == 2。
> ————————————————
> 版权声明：本文为CSDN博主「StarJava_」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/qq_35425070/article/details/106883833

這樣我們設定在 Project目錄底下設定`.gitattributes`，如下:

```
# 中文
*.java  diff=cp950
*.sql   diff=cp950
*.xml   diff=cp950
*.html  diff=cp950
*.xsl   diff=cp950
*.css   diff=cp950
*.cpp   diff=cp950
*.hpp   diff=cp950
*.c     diff=cp950
*.h     diff=cp950
```

### Git add -p 行數會有問題(未解)

Git add -p 行數會有問題

## 其他(彩蛋)

[windows下git中文乱码解决方式【图文】_wx60bf0f6c32435_51CTO博客](https://blog.51cto.com/u_15262460/2882761)

[Git Diff中文乱码问题 (windows git bash)_qhexin的专栏-CSDN博客](https://blog.csdn.net/qhexin/article/details/107848679)