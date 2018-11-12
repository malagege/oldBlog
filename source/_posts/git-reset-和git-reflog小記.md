---
title: git reset 和git reflog小記
date: 2018-11-09 20:37:22
tags: [git]
categories: Git
---

最近在改程式，所以我重新上一個commit`git commit --amend`
但做完發現...，我忘記做`git add xxx`了
當下`Ctrl+C`都不能中斷
只能QQ`:q!`or`:qa!`


<!--more-->

這種事當然要找一下解法

## git reflog

`git reflog`指令可以看最近刪除commit，之後會被自動刪除
可依照commit說明去找上次的內容

這時候想看reflog內容要怎麼看??
這時候要用之前學得融會貫通XD
`git checkout commit_id`
這樣就能切換過去
再`git diff`也能看到東西


## git reset 

`git reset --hard commit_id`

這樣就檢單還原

git reset --soft
git reset --mixed
不過這指令記不時起來

2018-11-12
[大師常來聊-高見龍談 Git 版控之final12345](https://youtu.be/Hl4tuzqaFJk?t=3843) 
剛好最近寫這篇，剛好沒說很熟git reset，腦還中對這個印象沒有運作的概念
今天剛好聽知這段，`goto,become`會比較容易理解

[大師常來聊-高見龍談 Git 版控之final12345 - rebase合併](https://youtu.be/Hl4tuzqaFJk?t=5999) 

[大師常來聊-高見龍談 Git 版控之final12345 - git flow ](https://youtu.be/Hl4tuzqaFJk?t=9141)

[大師常來聊-高見龍談 Git 版控之final12345 - git 沒模組化很容易衝突](https://youtu.be/Hl4tuzqaFJk?t=7509)
在沒做模組拆分很容易發生衝突，如:index.html

[大師常來聊-高見龍談 Git 版控之final12345 - git checkout file 跟 git reset HEAD --mixed 差別](https://youtu.be/Hl4tuzqaFJk?t=7887)

[大師常來聊-高見龍談 Git 版控之final12345 - git PR 遇到衝突](https://youtu.be/Hl4tuzqaFJk?t=8806)

其實下面兩個問題是我問題，希望之後實務上會用git更順利
基本上有基礎，再看那個直播會更容易了解
會看那個直播是剛好有買`為了你自己學git`，覺得書中內容不錯
剛好參加Kuro研討會順便去天龍書店逛的
好書!!就直接買了XD



FB有看到簡易了解圖示
[謝孟哲 - [git reset ...] 問題很簡單，有辦法把 cache 的內容寫回 working directory 嗎？...](https://www.facebook.com/photo.php?fbid=2418041491555969&set=gm.1944599088922083&type=3&theater&ifg=1)

{% asset_img 1.jpg 簡易圖示 %}


一直以為`git reflog`很難，一直沒有跨出這一步...
但是今天遇到這個狀況，卻不小心邁進一大步XD
