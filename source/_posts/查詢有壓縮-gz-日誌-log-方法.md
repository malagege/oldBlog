---
title: 查詢有壓縮(gz)日誌 log 方法
date: 2021-01-17 23:17:23
tags: [gz,log]
categories: Linux 
---

常常要抓下來再解縮查詢
這樣常常花了滿多時間


<!--more-->

```bash
gunzip -c mygzfile.gz | grep "string to be searched"
# 推薦使用這個，想看到哪一個檔案比較方便
zgrep "pattern" file.gz
zgrep "power" messages.*.gz
```

參考來源:
[linux - how to search for a particular string from a .gz file? - Stack Overflow](https://stackoverflow.com/questions/24218032/how-to-search-for-a-particular-string-from-a-gz-file)

不過還是建議大檔案不要用這種方式查詢
還不確定會不會造成什麼不良影響

彩蛋
有空實作:
[cron - Configuring logrotate without root access (per user log rotation) - Ask Ubuntu](https://askubuntu.com/questions/405663/configuring-logrotate-without-root-access-per-user-log-rotation)
[Docker安全自动化扫描工具对比测试 - 程序员大本营](https://www.pianshen.com/article/491436071/)
[如何优雅的在 Docker 容器中指定用户及组权限的三种方式 - 奇妙的 Linux 世界](https://www.hi-linux.com/posts/44367.html)