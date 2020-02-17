---
title: 排程 crontab 時間設定月底執行方法
date: 2019-08-04 14:48:00
tags: [linux]
categories: Linux
---

最近專案需要設定排程
主機上面的時區是 UTC+0 時間
但我們需要照 GMT+8 執行
排程往前面推-8 小時
但這時候遇到一個問題了
設定月初時間會跨前一天問題

<!--more-->

## 排程設定 L 字符

`0 16 L * * *`

有些排程工具是支援的 (spring, quartz)
LINUX crontab 不支援

## shell 程式執行判斷

最近專案需要在 Rancher 設定
所以上面的方法無法用
有參考使用[Crontab 每月最後一天執行 Shell Script](https://www.opencli.com/linux/crontab-run-on-last-day-of-month)
可以完成，範例如下

```bash

# 沒有環境變數，為true
if [ -z "$MODE" ]; then
    php .....
else
    # 時間用 0 0 28-31 * *
    # 日期加一天為1，就算是今日事月底最後一天
    DAY=$(date -d "$(date +%Y-%m-%d) +1 day" +%e)
    if [ "$MODE" = 'monthoflastday' ]; then
        if test $DAY -eq 1; then
            #  run the script here
            php ......
        fi;
    fi;
fi;
```

簡單說明就是 Rancher 要跑的 JOB 要多一個 `MODE` 環境變數
透過這個知道哪個要執行月底執行，有些不用

## crontab 簡單判斷方法

55 23 28-31 \* \* [[ "$(date --date=tomorrow +\%d)" == "01" ]] && myjob.sh
參考[automation - Cron job to run on the last day of the month - Stack Overflow](https://stackoverflow.com/questions/6139189/cron-job-to-run-on-the-last-day-of-the-month)

其他 date 設置可以參考
[Linux 使用 date 計算時間(昨天、明天) | Tsung's Blog](https://blog.longwin.com.tw/2010/07/linux-date-computer-2010/)
[linux 中用 shell 获取时间,日期 - ccmaotang - 博客园](https://www.cnblogs.com/tangtianfly/p/3457668.html)

## shell 跑時間迴圈方法

另外 shell 跑時間迴圈方法
[Bash: Looping through dates - Stack Overflow](https://stackoverflow.com/questions/28226229/bash-looping-through-dates)

```bash
d=2015-01-01
while [ "$d" != 2015-02-20 ]; do
  echo $d
  d=$(date -I -d "$d + 1 day")
done
```

其他參考來源:

- [automation - Cron job to run on the last day of the month - Stack Overflow](https://stackoverflow.com/questions/6139189/cron-job-to-run-on-the-last-day-of-the-month)
- [shell - How to check if a variable is set in Bash? - Stack Overflow](https://stackoverflow.com/questions/3601515/how-to-check-if-a-variable-is-set-in-bash)
- [Cron allowed special character "L" for "Last day of the month" on Debian - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/89188/cron-allowed-special-character-l-for-last-day-of-the-month-on-debian)
- [cron - Wikipedia](https://en.wikipedia.org/wiki/Cron#Format)
- [date - Jenkins -run job 2 days before end of every month - Stack Overflow](https://stackoverflow.com/questions/49295240/jenkins-run-job-2-days-before-end-of-every-month)
- [cron-last-sunday/run-if-today at master · xr09/cron-last-sunday](https://github.com/xr09/cron-last-sunday/blob/master/run-if-today)
- [鳥哥的 Linux 私房菜 -- 第十五章、例行性工作排程(crontab)](http://linux.vbird.org/linux_basic/0430cron.php#anacron_1)
- [linux下crontab与anacrontab的使用 - 小柒吃地瓜](https://www.i7dom.cn/179/2019/13/linux-cron-cycle.html)
