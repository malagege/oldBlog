---
title: PGP 筆記
date: 2021-04-11 23:32:06
tags: [java,pgp,gpg]
categories: Java
---


第一次使用 PGP加密筆記，原本看到 GPG 跟 Git 做簽章
想說GPG 跟 PGP 應該沒甚麼關係
但是後來爬文一下，GPG(GunPG)是產生和管理 PGP 金鑰軟體
看來有空順便來研究 GIT 簽章

## 測線上加密網站

https://github.com/Gaff/pgp.help
[pgp.help - Modern javascript client-side PGP encryption and decryption tool](https://pgp.help/#/)

[SmartNinja Online PGP](https://smartninja-pgp.appspot.com/)

[5 Online PGP Encryption Websites Free to Encrypt Email, Text](https://www.ilovefreesoftware.com/21/featured/online-pgp-encryption-websites-free-encrypt-email-text.html)

[PGPTool - use this tool to encrypt and decrypt PGP files. OpenPGP compatible](https://pgptool.github.io/)

## 測試 pgp 加解密

簡單來講，就次把private key 和 public key 匯入到 gpg
然後使用


```bash=
gpg --import {private/public key}

# 加密：

# recipient指定接收者的公鑰ID
gpg --recipient {keyid/uid} --output encrypt.txt --encrypt input.txt
# 也可以按喜好加上--armor選項等

# 我更喜歡用 
gpg  -se  -o  encrypt.txt  -r  {keyid/uid}   input.txt  
# s代表簽名  e代表加密
# o是 將結果 輸出到檔案  encrypt.txt
# r後面跟 接收者的 uid或者 key id， 接收者的公鑰必須已經匯入過
# input.txt 是你要加密的檔案


# 解密：
gpg  --output decrypt.txt  --decrypt encrypt.txt 
# 也可以
gpg -d encrypt.txt   # 輸出到終端 直接檢視
```
[2021年，用更現代的方法使用PGP（上）_UCloud雲端計算 - MdEditor](https://www.mdeditor.tw/pl/gW1n/zh-tw)
後來發現原始文章，放在下面

[[GnuPG] gpg 金鑰管理 · Hello, World!](http://pre.tir.tw/008/blog/output/gnupg-gpg-jin-yao-guan-li.html)

[GPG(GunPG)的使用 | Bolog](https://zhoubofsy.github.io/2017/05/18/security/gpg-usage/)

[PGP，OpenPGP和GnuPG加密之間的區別 – 易璽科技 ECNETWORKER](https://www.ecnetworker.com/2019/08/26/pgp%EF%BC%8Copenpgp%E5%92%8Cgnupg%E5%8A%A0%E5%AF%86%E4%B9%8B%E9%96%93%E7%9A%84%E5%8D%80%E5%88%A5/)



## 看起來實用工具

[Java GPG解密关于:encrypted message contains a signed message - not literal data.的解决_程序地带](https://www.secn.net/article/107917.html)

[Kleopatra - OpenPGP](https://www.openpgp.org/software/kleopatra/)

[Gpg4win - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/Gpg4win)

    GnuPG: 核心加密工具
    Kleopatra: 用於OpenPGP與X.509的憑證管理。
    GPA: 另一個可選的憑證管理
    GpgOL: Microsoft Outlook外掛程式，用於電子郵件加密
    GpgEX: Windows Explorer外掛程式，用於檔案加密
    Gpg4win Compendium: Gpg4win2的文件
    
[GnuPG - Software list](https://gnupg.org/related_software/swlist.html)
    
    
## 更多 GPG 教學

目前只有使用加解密跟 GIT 簽章
不過好像還有更進階用法
但是...目前好像用不太到
先預留一下爬到不錯文章

[2021年，用更现代的方法使用PGP（上） - C的博客 |UlyC](https://ulyc.github.io/2021/01/13/2021%E5%B9%B4-%E7%94%A8%E6%9B%B4%E7%8E%B0%E4%BB%A3%E7%9A%84%E6%96%B9%E6%B3%95%E4%BD%BF%E7%94%A8PGP-%E4%B8%8A/)
[備份圖](https://imgur.com/wkvbboi.png)

[2021年，用更现代的方法使用PGP（中） - C的博客 |UlyC](https://ulyc.github.io/2021/01/18/2021%E5%B9%B4-%E7%94%A8%E6%9B%B4%E7%8E%B0%E4%BB%A3%E7%9A%84%E6%96%B9%E6%B3%95%E4%BD%BF%E7%94%A8PGP-%E4%B8%AD/) [備份圖](https://imgur.com/LWYWSsm.png)

[2021年，用更现代的方法使用PGP（下） - C的博客 |UlyC](https://ulyc.github.io/2021/01/26/2021%E5%B9%B4-%E7%94%A8%E6%9B%B4%E7%8E%B0%E4%BB%A3%E7%9A%84%E6%96%B9%E6%B3%95%E4%BD%BF%E7%94%A8PGP-%E4%B8%8B/) [備份圖](https://imgur.com/V07AWzh.png)



    
## InvalidKeyException: Illegal key size(測試解決成功)

要安裝 JCE，原本不相信，但真的測試嚇一跳。還真的要安裝...
PS: 我專案是Java1.7，1.8竟然可以跑

![](https://i.imgur.com/XJscXex.png)

[InvalidKeyException: Illegal key size异常解决方案 – 思码老徐](https://www.xuchengen.cn/451)


[关于PGP解密基于Java Bouncy Castle_-0.0-的博客-CSDN博客](https://blog.csdn.net/weixin_44102477/article/details/88717815)


[新增 Git GPG Key 增加安全性 - Askie's Coding Life](https://askie.today/git-pgp-keys/)