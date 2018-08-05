---
title: git同步分支衝突解決方法
date: 2018-06-02 21:41:17
tags: [git]
categories: Git
---

之前副理叫我push上去他加入的gitlab空repo
我push竟然跑出`fatal: refusing to merge unrelated histories`
好在Google一下有找到答案

<!--more-->

```sh
git pull origin master --allow-unrelated-histories
```

不過後來有想到一個方法
就是直接下載那個git clone
然後copy檔案到那個clone下來就直接commit

但這缺點就不法把之前所有commit帶進去
看來還是用Google找的方法

參考來源
* [第 24 天：使用 GitHub 遠端儲存庫 - 入門篇 -- 範例照打無法成功 · Issue #31 · doggy8088/Learn-Git-in-30-days · GitHub](https://github.com/doggy8088/Learn-Git-in-30-days/issues/31)
* [git push - Git: which is the default configured remote for branch? - Stack Overflow](https://stackoverflow.com/questions/4847101/git-which-is-the-default-configured-remote-for-branch)
* [解决Git refusing to merge unrelated histories问题 | Doublemine](https://notes.wanghao.work/2017-06-28-%E8%A7%A3%E5%86%B3Git-refusing-to-merge-unrelated-histories%E9%97%AE%E9%A2%98.html)