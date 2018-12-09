---
title: gitea試玩合併分支(PR)
date: 2018-12-06 23:47:29
tags: git
categories: Git
---

最近試玩一下gitea
使用`choco install  gitea`
順利安裝成功
預設第一個申請帳號會是管理者

<!--more-->

直接進入主題

之前有在想PR後，分支會變成什麼狀況??
今天實驗用一般PR
合併PR會有兩條branch
{% asset_img 1.jpg 合併PR會有兩條branch %}


---------

## gitea 合併分支有兩個選項
PR還有兩種branch(rebase,squash)
{% asset_img 2.jpg 另外兩種分支 %}

稍微google一下
[使用 git rebase 提高 PR 质量 - 掘金](https://juejin.im/post/59c7b00df265da065e323acf)
{% asset_link web.png 備份圖 %}

~~不過我覺得PR在不傷commit情況下，一般合併應該就可以了~~

由於最近在想公司

## revert無法取笑merge branch

最近還發現revert不能取消merge branch
雖然我會用`git reset`
但還是有找了一下
[Git怎样撤销一次分支的合并Merge - SegmentFault 思否](https://segmentfault.com/q/1010000000140446)
[git/revert-a-faulty-merge.txt at master · git/git](https://github.com/git/git/blob/master/Documentation/howto/revert-a-faulty-merge.txt)
[git revert 讓提交不再害怕 - IT閱讀](https://www.itread01.com/content/1531335500.html)

有機會情境用到來測試看看...

## PR衝突解決方法

也有爬到PR可能會衝突解決辦法
~~不過還沒有實驗，先記錄~~

經過實驗得到很驚奇的結果
PR假如會發生衝突，gitea或gitlab不會讓你做合併動作(按鈕會變灰色的)
但要如何修正呢

當然就一般兩種

* merge 
先加入fork remote的資訊，在做`master`做`git pull add_remote_name`
正常會遇到衝突
就直接解這個問題(git merge --continue)
git push上去後，PR那個merge按鈕應該就可以按了

**但這個有嚴重的缺點，會自動產生merge commit**
其實這也不是缺點，保留當下時間的commit也只有這個方法

所以為了好看，大部分應該都會選擇rebase
[使用 git rebase 避免無謂的 merge | ihower { blogging }](https://ihower.tw/blog/archives/3843)

* rebase
先加入fork remote的資訊，在做`master`做`git pull --rebase add_remote_name`
正常會遇到衝突
就直接解這個問題(git rebase --continue)
正常你的repo應該只有你一個人
之前的commit會改變
可能需要`git push -f`
但有多人的話`git push --force-with-lease`
與多人衝突要怎麼辦??
這時候可能只能用`merge`方法，但如先前所說會有缺點
[Git 更安全的强制推送，--force-with-lease - walterlv](https://walterlv.com/post/safe-push-using-force-with-lease.html)

相關資料:
* [Github 發 Pull Request & 貢獻流程速查](https://gist.github.com/timdream/5968469#%E6%83%85%E5%A2%83pull-request-%E9%81%8E%E6%9C%9F%E7%84%A1%E6%B3%95%E5%90%88%E4%BD%B5)
* [Git 更安全的强制推送，--force-with-lease - walterlv](https://walterlv.com/post/safe-push-using-force-with-lease.html)
* [自己提交的 pull request 一直是 open，这个意思说一直在审核期吗？是不是说进入 close 了 就是审核通过了？ - V2EX](https://www.v2ex.com/t/184888)
(https://bitmingw.com/2017/02/16/git-merge-rebase-ours-and-theirs/)
* [Git: 更新分支+解衝突 | Summer。桑莫。夏天](https://cythilya.github.io/2018/05/27/git-merge-and-solve-conflict/)
* [Git: 比較 Merge Squash 與 Rebase Squash | Summer。桑莫。夏天](https://cythilya.github.io/2018/05/29/git-merge-squash-vs-rebase-squash/)
* [git pull --rebase vs git rebase : what's the danger? - Stack Overflow](https://stackoverflow.com/questions/38017517/git-pull-rebase-vs-git-rebase-whats-the-danger)

## git pull --rebase

這個東西非常玄
印象中感覺`Github Desktop`會做`git pull --rebase`
所以好像很少會產生merge branch commit

`git pull --rebase = git fetch + git rebase`

發現git rebase , git merge 合併預設remote可以省略
詳細裡面內容
* [Git: 更新分支+解衝突 | Summer。桑莫。夏天](https://cythilya.github.io/2018/05/27/git-merge-and-solve-conflict/)
* [Git: 比較 Merge Squash 與 Rebase Squash | Summer。桑莫。夏天](https://cythilya.github.io/2018/05/29/git-merge-squash-vs-rebase-squash/)
{% asset_link web2.png 備份圖 %}
還有Squash，感覺可以應用比較特別情境上面
先留著筆記


## PR  其實local端也可以做到

參考gitlab說明
{% asset_img 3.png gitlab上面local合併說明 %}
~~不過有好PR功能不用嗎o.<~~


## 怎麼快速了解git ours和theirs

查看裡面說明，講得很清楚
[化解冲突：git merge 与 git rebase 中的 ours 和 theirs | Ming's Blog](https://bitmingw.com/2017/02/16/git-merge-rebase-ours-and-theirs/)

> 可以看出，<<<<<<< 和 >>>>>>> 清晰地标示出了冲突的部分。你需要将它们改成期望的样子。如果你想保留两个版本中的一个而不引入新的修订，就可以使用两个预先定义的代词。<<<<<<< 和 ======= 之间的部分称为 ours, ======= 和 >>>>>>> 之间则称为 theirs. 此时，你只需要键入合适的指令即可化解冲突，而不需要进入文本编辑器。在当前项目可以得到如下运行结果

不過...，rebase是相反的
我這邊猜rebase因為是把`另外分支`為基準
所以自己的code可能算在theirs(自己猜測)

## git rebase小記

從開始commit開始做
舊的commit會從`上面依序往下排`
`git rebase -i --root`

* [沈弘哲 - YouTube](https://www.youtube.com/user/blue524326/videos)
* [git pull vs git pull --rebase - YouTube](https://www.youtube.com/watch?v=8h0K-2OaeSk)
* [git rebase interactive - reword - PART 1 - YouTube](https://www.youtube.com/watch?v=JhY0rR2wQq0&t=3s)
* [git rebase interactive - edit - PART 2 - YouTube](https://www.youtube.com/watch?v=TCKjQppHxxQ)
* [git rebase interactive - squash fixup - PART 3 - YouTube](https://www.youtube.com/watch?v=bfrZrbEHis0)
* [(20) git rebase interactive - exec drop - PART 4 - YouTube](https://www.youtube.com/watch?v=u8imRiiSyzk)


題外話，剛剛我筆電(elementaryos, git 2.7.x)做`git log`確沒有顯示HEAD跟remote資訊
查了一下，需要加`--decorate`
疑似之後2.13的git預設是`--decorate`
[git log - How to make git log decorate by default - Stack Overflow](https://stackoverflow.com/questions/21607305/how-to-make-git-log-decorate-by-default)