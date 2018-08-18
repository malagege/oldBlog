---
title: 兩個git repo合併在一起
date: 2018-08-16 20:32:46
tags: [git,merge]
categories: Git
---

最近接到一個程式修改，原本我以為沒有git
但是作完的時候，副理跟我說需要盒併他的git
網路找到相關資料，就結論而且不時適合做merge


<!--more-->
兩個git該如何合併呢?
[处理合并冲突](https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/merge-conflicts)
[git - 为什么要先commit，然后pull，最后再push？而不是commit然后直接push？ - SegmentFault 思否](https://segmentfault.com/q/1010000009549291)

[Is it possible to cherry-pick a commit from another git repository? - Stack Overflow](https://stackoverflow.com/questions/5120038/is-it-possible-to-cherry-pick-a-commit-from-another-git-repository)

1. add remote xxx url
2. git fetch xxx
3. git checkout -b ooo xxx/master
4. 可以做merge動作(但是可能會有很多衝突)

事後想想，應該我直接`git clone 對方git`
做合併應該比較快
就依對方做commit就好