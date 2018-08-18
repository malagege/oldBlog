---
title: git merge master 常常遇到衝突的雷
date: 2018-08-09 21:23:37
tags: [git]
categories: Git
---

最近之前學會fetch 跟merge 線上git分支
但最近開發偶爾會遇到衝突
而且還是幫我推git的人到正式環境裡面同樣我改的commit衝突(他用cherry-picker)
突然覺得我有什麼觀念不對

<!--more-->
PS:公司只有用master一條

就請叫我同事，他怎麼避免這個問題
他說他會開分支，但自己分支完不會合併到自己master上面
然後master不要做任何commit，他只做更新動作
分支都從master出來，改程式都需要在自己建立的分支
改完一樣給推git的人commit id

他更新完，就可以更新(pull) remote master分支
這樣不會衝突

PS:  cherry-pick出來的commit會不一樣

雖然這不是gitflow，但還是先了解git
知道使用方式才是正確的
等下次有機會使用新的專案
可以自己用用看
[Git怎麼這麼難用？Git Flow + 好習慣 = 不再苦惱 – Kuma老師的軟體工程教室 – Medium](https://medium.com/kuma%E8%80%81%E5%B8%AB%E7%9A%84%E8%BB%9F%E9%AB%94%E5%B7%A5%E7%A8%8B%E6%95%99%E5%AE%A4/%E5%9F%BA%E7%A4%8E-git-flow-%E5%B7%A5%E4%BD%9C%E6%B3%95-fa50b1dddc4f)


最後還煩腦commit message要怎麼寫才比較不會亂
很迷茫的去看vscode裡面的git，裡面分支好多又好亂(可能是我不會看)
Google有爬到好文[如何寫一個 Git Commit Message | louie_lu's blog](https://blog.louie.lu/2017/03/21/%E5%A6%82%E4%BD%95%E5%AF%AB%E4%B8%80%E5%80%8B-git-commit-message/)
裡面說可以去看linux和git
linux的git好大
所以我只看git
git分支不多，會看到很多一點做merge
對我而言是整齊的~~亂~~XD

GIT真的可以用很活，但是用不好感覺很容易爆炸
