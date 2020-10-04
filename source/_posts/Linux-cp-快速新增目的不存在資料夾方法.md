---
title: Linux cp 快速新增目的不存在資料夾方法
date: 2020-09-11 15:30:30
tags: [linux, cp]
categories: Linux
---

之前就有在查類似指令
但沒有覺得很快速方法
這邊先整理

<!--more-->

## mac cp --parents

可用這個指令解決我需求問題
但我沒 mac QQ

## mkdir -p

平常我快速建置資料夾
mkdir -p `dirname /xxxx/xx/x/x/x/x/filename`
cp xxx /xxxx/xx/x/x/x/x/filename

但數量越多，這樣做也不會比較快

## cp-p SOURCE... DIRECTORY/

```bash
cp-p() {
  last=${@: -1}

  if [[ $# -ge 2 && "$last" == */ ]] ; then
    # cp SOURCE... DEST/
    mkdir -p "$last" && cp "$@"
  else
    echo "cp-p: (copy, creating parent dirs)"
    echo "cp-p: Usage: cp-p SOURCE... DEST/"
  fi
}
```

但這樣改從清單還要多一層處理目的資料夾
我覺得不是很實用

## install -D

install -D test1.sh ./xxx/test.sh      

但不確定什麼問題，沒選出最佳答案
