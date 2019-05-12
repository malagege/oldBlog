---
title: 開啟大檔案的好用工具PilotEditLite
date: 2019-05-11 23:10:30
tags: [software]
categories: 實用工具
---

最近要匯入大量 sql
檔案有大到超過 3G
Sublime,vscode 都不適合開大檔案
所以網路爬了工具
記錄一下

<!--more-->

## PilotEdit

PilotEdit 有免費的!!但超過 400GB 就需要錢了(我有生之年會遇到??)
[PilotEdit Lite 12.6.0 中文版 - 支援超大檔案的文字編輯軟體 - 阿榮福利味 - 免費軟體下載](https://www.azofreeware.com/2015/06/pilotedit.html)

不過預設開啟檔案不是 UTF8
可能需要調整一下
`編輯`→`改變檔編碼`
可調整編碼

{% asset_img 1.png 調整編碼 %}

第二個選項一定要勾，第一個選項看你之後開的檔案編碼是不是一樣
假如是的話可以打勾

## 其他工具

EmEditor 要錢!!不是免費的
[EmEditor (文字編輯器) – 支持大檔案以及 Unicode 的最佳 Windows 編輯器!](https://zh-tw.emeditor.com/)

[windows 下如何查看超大文本文件(5G ) - 知乎](https://www.zhihu.com/question/19688121/answer/63245703)
裡面有提到

1. WINHEX
2. glogg

glogg 有時間再用用看
