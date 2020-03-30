---
title: git commit 指定幾行 & git stash 查看差異內容
date: 2018-11-14 20:40:10
tags: [git,git stash,]
categories: Git
---

最近使用Socuretree，發現大部分功能都看懂了
[Chocolatey Gallery | Sourcetree for Windows Enterprise 3.0.8](https://chocolatey.org/packages/SourceTree)
但是我發現stash，可以看到裡面存甚麼東西
Sourcetree還有git add 指定行數到index
這個看起來很方便
不知道原生怎麼處理
這邊筆記

<!--more-->

## git add -p

```
# To remove '-' lines, make them ' ' lines (context).
# To remove '+' lines, delete them.
# Lines starting with # will be removed
```
簡單來說
'+'不想被add進去，要移除單行內容(如:vi dd)
'-'不想被add進去，要取代' '(空白)(如:vi r 空白)

## git stash show -p
[Git diff against a stash - Stack Overflow](https://stackoverflow.com/questions/7677736/git-diff-against-a-stash)

這樣就能看到差異了

其實Sourcetree也真的算滿方便的東西
雖然之前用小烏龜，看log都很方便用了



## 番外vim筆記(單行上移/下移)方法

:m+ = 把目前這一行往下移動一行
:m-2 = 把目前這一行往上移動一行
[Vim的操作小技巧 | 高見龍](https://kaochenlong.com/2011/12/28/vim-tips/)
或者
ddkP  刪除單行 + 貼上  

[请问vim如何移动当前行向上或向下？不用选中 - V2EX](https://www.v2ex.com/t/49043)

多行我就沒有測試了
[[实践OK]vi行移动：vim上下移动一行或一段代码，及回到最近修改位置的vim快捷键，回到倒数第二次修改的位置，vim一下知道所在行的函数名设置方法。在文件中快速查找光标处一样的单词。 - 向东博客 专注WEB应用 构架之美 --- 构架之美，在于尽态极妍 | 应用之美，在于药到病除 - 赢在IT，Playin' with IT,Focus on Killer Application,Marketing Meets Technology.](https://jackxiang.com/post/5113/)
[在Vim，整行上下移动_vim_帮酷编程问答](http://hant.ask.helplib.com/vim/post_199787)

最近為了`git rebase`，發現vim不會上移一行....
特別找了一下

[Vim | 高見龍](https://kaochenlong.com/2011/12/26/vim/)
[爽爽快快學Vim(3) - Vim Plugins | 高見龍](https://kaochenlong.com/2012/06/01/screencast-3-vim-plugins/)

## stash apply 

> 要把 Stash 撿回來用，除了 pop 之外，另一個指令是 apply：
> 
> $ git stash apply stash@{0}
> 
> 這是指會把 stash@{0} 這個 Stash 拿來套用在現在的分支上，但 Stash 不會刪除，還是會留在 Stash 列表上。所以你可把 pop 指令看成「apply Stash + drop Stash」。

參考:[【狀況題】手邊的工作做到一半，臨時要切換到別的任務 - 為你自己學 Git | 高見龍](https://gitbook.tw/chapters/faq/stash.html)

## 重新上 remote 新的分支

最近分支命名錯誤，重新修改本地端分支名字
`git branch -m [ branc_name ] `
發現 `git push` 上到 remote 還是舊的名稱上面
原因是 `--set-upstream` 已經設定過了
必須重新修正
`git push -u [remote name] [branch name]`
可以下指令


> git push -u github master 指令可以拆解成以下的指令
> 
> $ git push github master
> $ git checkout master
> $ git branch -u github/master


## 取消 remote branch 追蹤

git branch --unset-upstream

取消當前分支

git branch --unset-upstream master  

也能指定分支