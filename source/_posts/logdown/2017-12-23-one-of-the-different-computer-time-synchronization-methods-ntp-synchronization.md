---
layout: post
title: '不同電腦時間同步方法之一(NTP 同步)'
date: 2017-12-23 15:36
comments: true
categories: Linux
tags: [NTP]
---
最近做生理監測設定，組長發現廠商時間上傳時間跟HIS不太合理
就是**測量時間**會大於**上傳時間**
這怎麼那麼奇怪!!!但用想也知道是設備跟Server不同步
這時候NTP就派上用場了
終於用在實務上

<!--more-->

```reg 時間同步20s.reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\DateTime\Servers]
@="0"
"0"="**IP**"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\W32Time\TimeProviders\NtpClient]
"DllName"=hex(2):25,00,73,00,79,00,73,00,74,00,65,00,6d,00,72,00,6f,00,6f,00,\
  74,00,25,00,5c,00,73,00,79,00,73,00,74,00,65,00,6d,00,33,00,32,00,5c,00,77,\
  00,33,00,32,00,74,00,69,00,6d,00,65,00,2e,00,64,00,6c,00,6c,00,00,00
"Enabled"=dword:00000001
"InputProvider"=dword:00000001
"AllowNonstandardModeCombinations"=dword:00000001
"CrossSiteSyncFlags"=dword:00000002
"ResolvePeerBackoffMinutes"=dword:0000000f
"ResolvePeerBackoffMaxTimes"=dword:00000007
"CompatibilityFlags"=dword:80000000
"EventLogFlags"=dword:00000001
"LargeSampleSkew"=dword:00000003
"SpecialPollInterval"=dword:00000014
"SpecialPollTimeRemaining"=hex(7):74,00,69,00,6d,00,65,00,2e,00,77,00,69,00,6e,\
  00,64,00,6f,00,77,00,73,00,2e,00,63,00,6f,00,6d,00,2c,00,37,00,64,00,37,00,\
  37,00,61,00,65,00,31,00,00,00,00,00,00,00,00,00,00,00,00,00,00,00,00,00,00,\
  00,00,00,00,00,00,00
```

```reg  time60s.reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\DateTime\Servers]
@="0"
"0"="**IP**"

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\W32Time\TimeProviders\NtpClient]
"DllName"=hex(2):25,00,73,00,79,00,73,00,74,00,65,00,6d,00,72,00,6f,00,6f,00,\
  74,00,25,00,5c,00,73,00,79,00,73,00,74,00,65,00,6d,00,33,00,32,00,5c,00,77,\
  00,33,00,32,00,74,00,69,00,6d,00,65,00,2e,00,64,00,6c,00,6c,00,00,00
"Enabled"=dword:00000001
"InputProvider"=dword:00000001
"AllowNonstandardModeCombinations"=dword:00000001
"CrossSiteSyncFlags"=dword:00000002
"ResolvePeerBackoffMinutes"=dword:0000000f
"ResolvePeerBackoffMaxTimes"=dword:00000007
"CompatibilityFlags"=dword:80000000
"EventLogFlags"=dword:00000001
"LargeSampleSkew"=dword:00000003
"SpecialPollInterval"=dword:0000003c
"SpecialPollTimeRemaining"=hex(7):74,00,69,00,6d,00,65,00,2e,00,77,00,69,00,6e,\
  00,64,00,6f,00,77,00,73,00,2e,00,63,00,6f,00,6d,00,2c,00,37,00,64,00,37,00,\
  37,00,61,00,65,00,31,00,00,00,00,00,00,00,00,00,00,00,00,00,00,00,00,00,00,\
  00,00,00,00,00,00,00

```

需要點登入檔案，重新開機才會生效。
這次經驗讓我知道設定NTP是很重要的事情
組長問我NTP時間一定會正確嗎？我後來網路查一下
NTP運算會扣除網路傳輸部份，所以不會有兩台電腦時間不一樣問題

* [The Will Will Web | 如何調整 Windows 作業系統的時間正確性 ( 終極指令版 )](https://blog.miniasp.com/post/2009/06/08/How-to-adjust-Time-using-Windows-Time-Service.aspx)
* [XYZ的筆記本: windows 時間自動校正](http://xyz.cinc.biz/2015/04/windows-w32tm.html)