---
title: SVN 移轉 GIT 小記
date: 2021-02-21 01:13:25
tags: [git]
categories: Git
---

最近公司 SVN Repository 要備廢除
由於先前案子是使用 SVN，此專案也是交接下來的
不希望把 SVN 修改記錄刪除
很多專案都是把檔案從丟新的到 GIT
但我不是這樣做
花了一點時間找工具轉
趕緊紀錄，因為沒有在家實作
所以大概季錄一下

<!--more-->

## 能使用工具

- subgit
- svn2git
- git svn

```bash
subgit import --non-interactive --default-domain xxxx.com.tw --svn-url https://IP/svn/project  project.git
```


原本要用 subgit ，但下載發現好像有期限
但執行照一般指令，沒有成功(但好像是SVN帳號或 我忘了連VPN)
sub2git 也跳出底程指令不能跑
我看他使用 git svn 就研究了一下
好像跳出帳號密碼驗證失敗

## SVN 輸入帳號轉 GIT

```bash
sudo apt-get install git-svn 
```

```bash
git svn clone  svn://server/repo  --username user
# svn: 可以改 https://
git svn clone https://IP/svn/xxx/oooo --username=xxx
```
輸入完跳出密碼就可以轉了
`-r1450:HEAD`
可以限制版本從哪邊開始



參考找的資料
[將 SVN Local Repository 轉換為 Git Local Repository (Windows) | 格物致知](https://amobiz.github.io/2014/09/07/convert-svn-local-repository-to-git-local-repository-windows/)

[[git]開始使用git - 用git操作svn repo @ Alan Tsai 的學習筆記｜A Microsoft MVP, MCT, Blogger, Youtuber and ASP .NET Developer Blog](https://blog.alantsai.net/posts/2017/06/git-svn-usage)

[使用 svn2git 從 SVN 遷移到 Git | 他山教程，只選擇最優質的自學材料](http://www.tastones.com/zh-tw/stackoverflow/git/migrating-to-git/migrate_from_svn_to_git_using_svn2git/)

[git svn clone --password pass gives “Unknown option: password” - Stack Overflow](https://stackoverflow.com/questions/21040553/git-svn-clone-password-pass-gives-unknown-option-password)

[git svn migration failing with svn 1.8 - Stack Overflow](https://stackoverflow.com/questions/27240428/git-svn-migration-failing-with-svn-1-8)

[How to git-svn clone the last n revisions from a Subversion repository? - Stack Overflow](https://stackoverflow.com/questions/747075/how-to-git-svn-clone-the-last-n-revisions-from-a-subversion-repository)