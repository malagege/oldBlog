---
title: 不留這次Linux bash 歷史記錄的方法
date: 2018-07-02 21:11:29
tags: [linux,bash,history]
categories: Linux
---

有時因為要測試指令
會大量留下不能用的指令到history上面
今天就搜尋一下，發現[XYZ的筆記本: Linux Bash 刪除 history 指令操作歷史紀錄](https://xyz.cinc.biz/2017/08/linux-bash-history-clear.html)
有講要怎麼清除，有測試成功!!!

<!--more-->

** 以下擷取[XYZ的筆記本: Linux Bash 刪除 history 指令操作歷史紀錄](https://xyz.cinc.biz/2017/08/linux-bash-history-clear.html) **
>只刪除這次登入後的操作紀錄：
方法一：清空 HISTFILE 變數內容，則登出時，不會將本次操作紀錄儲存到 HISTFILE 設定的檔案。
    # unset HISTFILE
方法二：將 HISTSIZE 設為0，下「history」指令時，也不會列出指令(記憶體中沒存放指令紀錄?)，則登出時，不會更新記錄檔內容(?)。
(註：參考資料是寫會刪除全部紀錄，但我測試是只有不會儲存本次操作紀錄)
    # HISTSIZE=0
 方法三：強制刪除本次登入 Bash 的 PID，則本次操作紀錄，不會儲存到記錄檔。
    # kill -9 $$

感覺最實用是方法3`kill -9 $$`

清除所有history發法
```
# history -c
# history -w
```
詳細可以去看一下鳥哥
[鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php#history)

另外還有HISTCONTROL方法
[Linux Bash環境下，輸入指令不留痕跡的作法 :: 哇哇3C日誌](https://ez3c.tw/1717)


今天還看到HISTORY可以用Ctrl+R 找過去打的指令
[Linux 指令歷史紀錄（History）的操作教學與範例 - G. T. Wang](https://blog.gtwang.org/linux/mastering-linux-command-line-history/)

參考來源:
[XYZ的筆記本: Linux Bash 刪除 history 指令操作歷史紀錄](https://xyz.cinc.biz/2017/08/linux-bash-history-clear.html)
[鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php#history)
[Linux Bash環境下，輸入指令不留痕跡的作法 :: 哇哇3C日誌](https://ez3c.tw/1717)
[Linux 指令歷史紀錄（History）的操作教學與範例 - G. T. Wang](https://blog.gtwang.org/linux/mastering-linux-command-line-history/)
