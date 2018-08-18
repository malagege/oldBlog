---
title: linux 設定環境變數
date: 2018-08-09 21:27:49
tags: [linux,source,env,export]
categories: Linux
---

最近同事在測試環境玩GO語言
他好像在linux沒法執行編譯程式
[golang 跨平台編譯——go 在windows上編譯Linux平台的程序（Cross Compilation from Windows to Linux/Ubuntu） - 牛頓的小腦 - 博客園](https://www.cnblogs.com/oxspirt/p/7072818.html)
他是在window編譯完後在linux上面不能跑
後來我發現他沒有執行`chmod a+x hello`
所以不能執行
最後他試著在linux上面放go語言
設定環境變數有點況狀
當然要趁著這個機會好好了解XD

<!--more-->

有設定`export PATH=$PATH:/xxx/go/bin`
但是登出後  就會被清除

來自[鳥哥的 Linux 私房菜 -- 第十章、認識與學習BASH](http://linux.vbird.org/linux_basic/0320bash.php#fig10.2.3)

要永久存放參數，可以放在`~/bash_profile`
假如要所有人登入能跑的話可以放在`/etc/profile`
詳細可參考鳥哥裡面的圖片[shell執行順序](http://linux.vbird.org/linux_basic/0320bash.php#fig10.4.1)

source
之前寫超級終端機文章有寫到source
[超級終端機 zplug | 程式狂想筆記](https://malagege.github.io/blog/2018/01/22/logdown/2018-01-22-5198665/)

>由於 /etc/profile 與 ~/.bash_profile 都是在取得 login shell 的時候才會讀取的設定檔，所以， 如果你將自己的偏好設定寫入上述的檔案後，通常都是得登出再登入後，該設定才會生效。那麼，能不能直接讀取設定檔而不登出登入呢？ 可以的！那就得要利用 source 這個指令了！
利用 source 或小數點 (.) 都可以將設定檔的內容讀進來目前的 shell 環境中！ 舉例來說，我修改了 ~/.bashrc ，那麼不需要登出，立即以 source ~/.bashrc 就可以將剛剛最新設定的內容讀進來目前的環境中！很不錯吧！還有，包括 ~/bash_profile 以及 /etc/profile 的設定中， 很多時候也都是利用到這個 source (或小數點) 的功能喔！
[鳥哥的 Linux 私房菜 -- source ：讀入環境設定檔的指令](http://linux.vbird.org/linux_basic/0320bash.php#source)

env
看環境變數用

## cmd1 && cmd2與  cmd1 ||cmd2
之前有寫過[連續打指令&& 與;差別 | 程式狂想筆記](https://malagege.github.io/blog/2018/03/26/logdown/2018-03-26-7062187/)

### cmd1 && cmd2
1. 若 cmd1 執行完畢且正確執行($?=0)，則開始執行 cmd2。
2. 若 cmd1 執行完畢且為錯誤 ($?≠0)，則 cmd2 不執行。

### cmd1 ||  cmd2
1. 若 cmd1 執行完畢且正確執行($?=0)，則 cmd2 不執行。
2. 若 cmd1 執行完畢且為錯誤 ($?≠0)，則開始執行 cmd2。

來自[鳥哥的 Linux 私房菜 -- $? (指令回傳值) 與 && 或 ||](http://linux.vbird.org/linux_basic/0320bash.php#fig10.5.2)

$?是指令回傳錯誤碼
&& cmd1執行錯誤就不會執行cmd2
**|| cmd1執行錯誤就會執行cmd2，相反cmd1執行正確cmd2就不會執行**
這個好特別!!!
