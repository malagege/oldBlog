---
title: git difff 出現old mode 100755 new mode 100644
date: 2019-04-24 23:03:07
tags:
categories:
---

最近遇到 git 沒有變動的檔案
發現狀態跑出更動檔案
找了一下解決方法

<!--more-->

```
$ git diff code.c
old mode 100755
new mode 100644
```

這原因應該是 Linux、Window、Mac 之間檔案複製到不同台電腦做 git commit
可能會遇到的問題

`git config core.filemode false`
解決問題

在看情形是不是要全域做設定

參考來源:

- [git tips: 设置 filemode，避免 NTFS 文件权限变更引起的修改 - 简书](https://www.jianshu.com/p/3b0a9904daca)
- [git file mode change - 晓寒的个人空间 - OSCHINA](https://my.oschina.net/xhan/blog/493454)
- [git gui - How do I remove files saying "old mode 100755 new mode 100644" from unstaged changes in Git? - Stack Overflow](https://stackoverflow.com/questions/1257592/how-do-i-remove-files-saying-old-mode-100755-new-mode-100644-from-unstaged-cha)
- [git file mode change - 晓寒的个人空间 - OSCHINA](https://my.oschina.net/xhan/blog/493454)

最近沒有時間整理這些資料
先預留 NOTE:
(sharding DB 不太能做 join)

- [数据库 Sharding 的基本思想和切分策略 - Laurence 的技术博客 - CSDN 博客](https://blog.csdn.net/bluishglc/article/details/6161475)
- [MySQL Sharding 详解 - 简书](https://www.jianshu.com/p/6ebafabfddae)
- [MySQL 分库分表，写得太好了！ - 51CTO.COM](http://database.51cto.com/art/201809/583857.htm)
- [MySQL 上 sharding 的方案 | Gea-Suan Lin's BLOG](https://blog.gslin.org/archives/2017/02/02/7113/mysql-%E4%B8%8A-sharding-%E7%9A%84%E6%96%B9%E6%A1%88/)
- [Docker 学习笔记\_08 使用 Rancher pipeline 搭建基于容器的 CICD | 一个 DBA 的工作学习笔记](http://dbase.cc/2018/02/27/docker/08_%E4%BD%BF%E7%94%A8Rancher-pipeline%E6%90%AD%E5%BB%BA%E5%9F%BA%E4%BA%8E%E5%AE%B9%E5%99%A8%E7%9A%84CICD/)
- [hexo 个人 next 主题博客接入谷歌广告 | Daryl's Blog](https://darylliu.github.io/2019/04/12/hexo%E4%B8%AA%E4%BA%BAnext%E4%B8%BB%E9%A2%98%E5%8D%9A%E5%AE%A2%E6%8E%A5%E5%85%A5%E8%B0%B7%E6%AD%8C%E5%B9%BF%E5%91%8A/)
