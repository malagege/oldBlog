---
layout: post
title: 'git建立空分支'
date: 2017-12-23 15:53
comments: true
categories: Git
tags: [git,branch]
---
今天看到git網路上寫的，發現它可以建立新空分支。
因為不常使用，在此筆記XD

```sh
git checkout --orphan gh-pages //建立一個沒有parent的branch，並移到該branch上
```

[建立自己的GitHub Project Pages | kpman | code](https://code.kpman.cc/2013/05/18/%E5%BB%BA%E7%AB%8B%E8%87%AA%E5%B7%B1%E7%9A%84github-project-pages/)
[在GIT中创建一个空分支 - Min的专栏 - SegmentFault](https://segmentfault.com/a/1190000004931751)