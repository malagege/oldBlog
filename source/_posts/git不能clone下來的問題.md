---
title: git不能clone下來的問題
date: 2019-03-16 20:12:57
tags: [git]
categories: Git
---

網路找到很多這個解法

<!--more-->

## HTTPS 解除 SSL 憑證檢查

### 設定全域不檢查方法

```bash
git config --global http.sslVerify false
```

### 設定當下不檢查 SSL 方法

原本想說看有沒有暫時的語法
後來找到這個

```
1.创建临时环境变量：

windows上命令行输入：

set GIT_SSL_NO_VERIFY=true git clone

Linux下：

env GIT_SSL_NO_VERIFY=true git push
```

但我的 window 的 git bash 上面指令都不管用
不過我後來找到一篇用

```bash
export GIT_SSL_NO_VERIFY=1
```

可以解決

[执行 Git 命令时出现各种 SSL certificate problem 的解决办法 - officercat 的专栏 - CSDN 博客](https://blog.csdn.net/officercat/article/details/39989837)
[git - How do I set GIT_SSL_NO_VERIFY for specific repos only? - Stack Overflow](https://stackoverflow.com/questions/9008309/how-do-i-set-git-ssl-no-verify-for-specific-repos-only)
[Git ssl error on windows - Stack Overflow](https://stackoverflow.com/questions/7526182/git-ssl-error-on-windows)

### 改成 SSL 方法也能解決這個問題

另外，還有一種方法就是改成用 ssh 方法
git clone 也能解決這個問題
