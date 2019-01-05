---
title: 快速打包程式的腳本(shell)
date: 2019-01-05 17:45:52
tags: [shell, 打包程式]
categories: shell
---

最近公司做一個專案需要手動打包程式
因為目前公司測試環境只有一個
所以那個專案放在我們公司某一個 GIT 裡面
所以很難做到用`git archive`打包動作...

<!--more-->

最近因為很急著上 code
卻不小心包錯(手動複製到別的資料夾)...
~~人會犯錯是應該的~~
但同一個錯誤應該是要避免的
所以我寫了這個 shell
project_path 前面路徑請注意要更改
pack_path 前面路徑看是否需要調整
window 請使用 git bash 執行，直接點兩下好像不能跑

```sh
## 快速打包程式
# project_path 前面路徑請注意要更改
# pack_path 前面路徑看是否需要調整
# window請使用git bash執行，直接點兩下好像不能跑
project_path=/d/GIT/專案名稱/
pack_path=/d/pack/`date +%Y%m%d`/專案名稱/
echo project_path
echo $project_path
echo pack_path
echo $pack_path
rm -rf $pack_path
mkdir -p $pack_path

filelist=(
#請放置相對程式路徑
application/controllers/*.php
application/controllers/A/ABC.php
)


for file in ${filelist[@]};do
mkdir -p `dirname $pack_path$file`
dir=(`dirname $pack_path$file`)
# echo cp $project_path$file ${dir[0]}
cp -f $project_path$file ${dir[0]}
done
```

其實我也不太會寫 shell
這個只是簡單的應用
我只 Google 一下 shell 怎麼寫 Array 跟抓檔案目前資料夾路徑(dirname)
在簡單用 mkdir, cp 就做出來這個東西
從此打包程式不手殘
