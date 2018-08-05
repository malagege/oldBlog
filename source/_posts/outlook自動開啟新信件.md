---
title: outlook自動開啟新信件
date: 2018-06-24 00:33:38
tags: [outlook,自動化]
categories: 寫程式一些事
---

現在工作每天都要寫工作進度
有時候小小一個動作
可以省很多時間做調整

這個bat是網路找的...


<!--more-->

```bat
REM 取得今天的年、月、日 (自動補零)
SET TodayYear=%date:~2,2%
SET TodayMonthP0=%date:~5,2%
SET TodayDayP0=%date:~8,2%

REM 取得今天的年、月、日 (純數字)
REM 2010/08/03 更新：以下是為了修正 Batch 遇到 08, 09 會視為八進位的問題
IF %TodayMonthP0:~0,1% == 0 (
	SET /A TodayMonth=%TodayMonthP0:~1,1%+0
) ELSE (
	SET /A TodayMonth=TodayMonthP0+0
)

IF %TodayMonthP0:~0,1% == 0 (
	SET /A TodayDay=%TodayDayP0:~1,1%+0
) ELSE (
	SET /A TodayDay=TodayDayP0+0
)

echo 日期 %TodayYear%/%TodayMonthP0%/%TodayDayP0%
pause
"C:\Program Files (x86)\Microsoft Office\Office12\outlook.exe" /c ipm.note /m "mailto:'xxxxx'<ooooooo>;'xxxxx' <oooooo>;&subject=ZeYi-%TodayYear%%TodayMonthP0%%TodayDayP0% 工作回報&body=Dear all:"
```

參考來源：

[The Will Will Web | 如何在 Batch 檔取得系統的日期、時間欄位 (第三版)](https://blog.miniasp.com/post/2009/11/03/How-to-get-system-date-time-in-batch-file-part-III.aspx)


[How to create an Outlook new email with specified command line?](https://www.extendoffice.com/documents/outlook/5277-outlook-new-email-command-line.html)