---
title: 常搞錯GIT觀念(for me)
date: 2018-08-09 21:19:40
tags: [git]
categories: Git
---

先留個常忘記GIT觀念
以免忘記XD

* 常常最後修改檔案，做git add。但再次異動也要再做`git add`動作
* 剛建立branch，兩個branch都在同一條上面(應該說是同一個點)
* `git add` 要怎麼取消
git reset HEAD
OR
git checkout -- filename
* git branch要怎麼修改名稱
git branch -m oldName newName
* 新分之要先設好預設的remote
git  --set-upstream-to origin/newName
[git修改分支名称 - 简书](https://www.jianshu.com/p/cc740394faf5)
* 分支是point指標概念 (可以看上面第二點)
* 分支刪除掉commit不會不見
* 刪除沒有合並的分支，`gitk`commit是看不到的，但他過很久會進入git垃圾回收
* 刪除合並的分支，`gitk`commit還是回流下來
* master,develop盡量用merge，一個需求開一個feature，然後完成可以與develop做合併(可參考gitflow)
* `git remote`有點像網路上的git分支，fetch完可以在與本地做merge動作
* 不同git來源也能加入別的git repo(git remote add xxx http://xxxx)

不支援大小寫
Git 修改檔名大小寫
$ git config core.ignorecase false
$ git mv -f abc.php Abc.php
[XYZ的筆記本: Git 修改檔名大小寫](https://xyz.cinc.biz/2016/01/git-rename-case-sensitive.html)
