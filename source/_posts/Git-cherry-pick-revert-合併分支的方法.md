---
title: 'Git cherry-pick, revert 合併分支的方法'
date: 2019-08-21 20:57:43
tags:
categories:
---

最近想到 revert , cherry-pick 分支好像不能做
但只細看一下錯誤訊息
好像能使用

<!--more-->

```sh
git revert -m 1 merge_commit_id
```

`-m` 這個就是用在 (複製/退回)branch 用的
1 怎麼來的?



> 如上面的例子中，從 git show 命令的結果中可以看到，merge commit 的 parent 分別為 ba25a9d 和 1c7036f，其中 ba25a9d 代表 master 分支（從圖中可以看出），1c7036f 代表 will-be-revert 分支。需要注意的是 -m 選項接收的參數是一個數字，數字取值為 1 和 2，也就是 Merge 行裡面列出來的第一個還是第二個。
> 我們要 revert will-be-revert 分支上的內容，即 保留主分支，應該設置主分支為主線，操作如下：
> ➜ git revert -m 1 bd86846
[Git 之 revert (撤銷commit或merge) - 我是guyue，guyue就是我O(∩_∩)O - CSDN博客](https://blog.csdn.net/guyue35/article/details/82703065)


這邊 git show 指令可以看到兩個 merge commit_id 
通常有兩個， 1 ,2 照順序
```
➜  git show bd86846
commit bd868465569400a6b9408050643e5949e8f2b8f5
Merge: ba25a9d 1c7036f
```

這邊我有想到一個問題
revert branch 要怎麼用回來呢
繼續 revert ?  後來我覺得應該要再 merge  commit id 就可以合併

但是看到這篇 revert commit 再次merge 可能需要注意的事情
[Git 之 revert - 活到老学到老 - SegmentFault 思否](https://segmentfault.com/a/1190000012897697) {% asset_link web1.png 備份圖 %}
這篇值得看


相關連結:
* [Git怎样撤销一次分支的合并Merge - SegmentFault 思否](https://segmentfault.com/q/1010000000140446)
* [git revert merge 的commit - 杨博客 - 博客园](https://www.cnblogs.com/520yang/articles/6732687.html)
* [Git 之 revert - 活到老学到老 - SegmentFault 思否](https://segmentfault.com/a/1190000012897697)
* [git cherry-pick -m 例子 - SegmentFault 思否](https://segmentfault.com/q/1010000010185984)
