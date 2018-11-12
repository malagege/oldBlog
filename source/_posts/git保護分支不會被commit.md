---
title: git保護分支不會被commit
date: 2018-10-11 21:30:01
tags: [git,git hook]
categories: Git
---

最近在pull master實候
竟然發現merge....
雖然不知道發生了什麼事情
但不應該這樣
後來找到是我在master commit!!!!!

<!--more-->

立馬不說，先壓縮一個檔案起來(裡面有程式還沒commit 怕怕)
先用`git reset  xxxx^ --hard`
順利還原!!!

未了怕悲劇發生，於是我開始找如何防止master commit
後來找到[Git Branch Warnings - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=teledemic.branch-warnings)
不過下載數不高，所以沒有使用

最後找到[git hooks - prevent commit to local master branch and prevent push to remote master branch](https://gist.github.com/aaronhoffman/ffbfd36928f9336be2436cffe39feaec)
```sh

#!/bin/sh
# prevent commit to local master branch

branch=`git symbolic-ref HEAD`
if [ "$branch" = "refs/heads/master" ]; then
    echo "pre-commit hook: Can not commit to the local master branch."
    exit 1
fi

exit 0
```

最後修改

```sh

#!/bin/sh
# prevent commit to local master branch

branch=`git symbolic-ref HEAD`
if [ "$branch" = "refs/heads/master" ] || [ "$branch" = "refs/heads/mixed" ]; then
    echo "pre-commit hook: Can not commit to the local master/mixed branch."
    exit 1
fi

exit 0
```

放在`.git/hook/pre-commit`就完成

