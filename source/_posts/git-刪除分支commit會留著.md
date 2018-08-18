---
title: git 刪除分支commit會留著?!
date: 2018-08-09 21:26:21
tags: [git]
categories: Git
---

最近公司master merge一直出問題
有一直想到feature一直建立分支，需求爆多那不分支不是之後會越積越多
一直找不到答案
最後在[30 天精通 Git 版本控管 (17)：關於合併的基本觀念與使用方式 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10138437)找到答案
>刪除不必要的分支
假設我們 feature 分支還會持續開發新功能，所以可以暫時將它留著，讓後續還能繼續切換到 feature 分支繼續開發。
如果你確定用不到的話，可以用 git branch -d feature 刪除該分支。
在 Git 裡，只要沒有執行過「合併」的分支，都不能用上述指令進行刪除，必須改用 git branch -D feature 才能刪除該分支。

<!--more-->

(在有merge過的分支)
刪除分支commit會留著
想建立回來刪除的branch
可以用checkout到之前的commit

(在沒有merge過的分支)
刪除分支
`git log --oneline --all`
沒有看到commit   囧
我原本實驗以為是真的把commit刪掉
但是看[【狀況題】不小心把還沒合併的分支砍掉了，救得回來嗎？ 為你自己學 Git](https://gitbook.tw/chapters/branch/restore-deleted-but-unmerged-branch.html)
說明commit 不會馬上被刪掉，但有機會之後被git垃圾清除
[【常見問題】合併過的分支要留著嗎？ 為你自己學 Git](https://gitbook.tw/chapters/branch/about-merged-branch.html)
我覺得是我需求完成結案就刪吧
又有需求再開新branch

使用git branch
建出來的branch會在你現在的commit
真的是很神奇東西

話說git我原本以為他是zip觀念
刪除整個commit都會不見
現在感覺他跟指標(point)很像(事後查到[【狀況題】不小心把還沒合併的分支砍掉了，救得回來嗎？ 為你自己學 Git](https://gitbook.tw/chapters/branch/restore-deleted-but-unmerged-branch.html)裡面有寫到)
等等，他好像跟tag屬性重疊XD
[關於版本控管系統的分支(branch)與標籤(tag)的區別 | Kenmingの鮮思維](https://www.kenming.idv.tw/about_vc_branch_tag_difference/)這邊有簡單介紹
用法不太一樣

但等等...怎麼知道有哪些branch已經合併
不可能用`gitk --all`肉眼看圖
爬文有找到[Git - 分支的管理](https://git-scm.com/book/zh-tw/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E7%AE%A1%E7%90%86)
查詢合併分支`git branch --merged`
查詢沒有合併分支`git branch --no-merged`


[建立 / 刪除分支 · Git](https://zlargon.gitbooks.io/git-tutorial/content/branch/create_delete.html)
[delete git branch local and remote (刪除本地跟遠端 git branch) | louie_lu's blog](https://blog.louie.lu/2016/08/26/delete-git-branch-local-and-remote-%e5%88%aa%e9%99%a4%e6%9c%ac%e5%9c%b0%e8%b7%9f%e9%81%a0%e7%ab%af-git-branch/)
[怎么用git clone 远程的所有分支 - 简书](https://www.jianshu.com/p/0fe715a7fbb3)