---
title: Shell 快速統計資料夾下底層檔案數量
date: 2019-11-12 21:26:38
tags: [shell,linux]
categories: Linux
---


```bash
for i in */ .*/ ; do      echo -n $i": " ;      (find "$i/" -maxdepth 1 -mindepth 1 -type d | wc -l) ;  done
```

mindepth 0 數字是指`.`本層開始搜尋的(包含.)自己，但通常會用 1(不包含 `.`)
maxdepth 1 數字是使`.`往後搜尋資料夾


```bash
for i in */ .*/ ; do      echo -n $i": " ;      (find "$i" -maxdepth 3 -type f -name "*.docx" | wc -l) ;  done
```

[Recursively counting files in a Linux directory - Stack Overflow](https://stackoverflow.com/questions/9157138/recursively-counting-files-in-a-linux-directory)



## find 確認資料夾是否有檔案

```bash
 if [ `find . -mindepth 1  | wc -l ` -gt 0 ]; then echo "test"; fi
```


## awk 指令

echo 1 2 3 4 5 | awk {print 1}


## 深入 find 指令

發現 find 指令順序也有關係


[find 实用程序](http://lifegoo.pluskid.org/wiki/UtilFind.html)
[Linux Find 命令精通指南](https://www.oracle.com/technetwork/cn/topics/calish-find-096463-zhs.html)
[find命令的几个例子解析(-a,-o,-path,-wholename,-prnue,-print) - 程序园](http://www.voidcn.com/article/p-bgvmodwd-pg.html)

