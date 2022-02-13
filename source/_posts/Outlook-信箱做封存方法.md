---
title: Outlook 信箱做封存方法
date: 2022-02-13 23:25:18
tags: [outlook, 封存]
categories: Outlook
---

Outlook 信箱做封存方法(writed)

最近公司筆電空間快爆炸了，有買一個 SSD 硬碟。但這個是外接式硬碟，想找可以手動作封存方法，特別紀錄一下操作流程。

<!--more-->

## 手動封存

參考:
- [手動存檔專案](https://support.microsoft.com/zh-tw/office/%E6%89%8B%E5%8B%95%E5%AD%98%E6%AA%94%E5%B0%88%E6%A1%88-ecf54f37-14d7-4ee3-a830-46a5c33274f6)
- [自動封存功能的描述 - Outlook | Microsoft Docs](https://docs.microsoft.com/zh-tw/outlook/troubleshoot/data-files/description-of-the-autoarchive-feature)


執行下列其中一項操作：

1. 在[Outlook 2013：按一下 [檔案>[>清理工具>檔案。

![](https://i.imgur.com/DhIpJs8.png)


   在 Outlook 2016 中：按一下 [檔案] > [資訊] > [工具] > [清理舊項目]
   ![](https://i.imgur.com/8wUGT0J.png)

2. 按一下 [封存此資料夾及所有的子資料夾] 選項，然後選擇您要封存的資料夾。

3. 在 [封存郵件早於] 下，輸入日期。

![](https://i.imgur.com/wS4X2Ko.png)

封存右下角處理動作，如圖。
![](https://i.imgur.com/npl6HiR.png)

### 外接硬碟封存卸除遇到狀況

選擇外接硬碟，假如拔除硬碟，開啟Outlook會跳出下圖，可按確定跳過。
![](https://i.imgur.com/HTN3MaO.png)

再按下取消就可以跳過。

![](https://i.imgur.com/Zzr40gQ.png)


當然也可以拔掉pst設定，但這樣做封存又要新增，所以我就沒拔除了。


## 做完釋放 Outlook 空間

完成封存發現空間還是沒有改變，後來查看一下文章，發現要做壓縮才能釋放空間。

> 做完封存或刪除郵件後, 記得要做壓縮.
封存或刪除的資料空間才會真正的被移除, 檔案才會變小.
> 
> https://support.microsoft.com/zh-tw/kb/2241478

參考:[怎麼減少outlook個人資料夾pst檔大小 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/questions/10183483)


## 自動封存處理方法

可以在「收件夾」內容設定啟用自動封存或停用。子收件夾也可以做設定

![](https://i.imgur.com/yyvVQvV.png)

收件夾內容設定自動封存選項

![](https://i.imgur.com/yz179CR.png)


可參考:[自動封存Outlook 郵件，不怕信箱容量爆掉 | T客邦](https://www.techbang.com/posts/7440-microsoft-exchange-e-mail-drj-insufficient-capacity)