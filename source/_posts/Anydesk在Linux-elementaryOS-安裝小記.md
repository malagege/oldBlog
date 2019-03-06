---
title: Anydesk在Linux(elementaryOS)安裝小記
date: 2019-02-13 21:16:49
tags: [anydesk, remoted desktop]
categories: 實用工具
---

最近買 SSD 升級筆電
重新灌了 ElementaryOS
但發現之前 Anydesk 沒有記錄筆記
都會一直忘記 deb 要怎麼安裝
今天小記一下

<!--more-->

有爬到[手動安裝](https://help.ubuntu.com/kubuntu/desktopguide/zh_TW/manual-install.html)

```
安裝某個.deb檔案，簡單的在.deb檔案上Right單擊游標，然後選擇Kubuntu Package Menu->安裝軟體套件。
或者，你可以在終端下輸入如下內容來安裝.deb檔案
sudo dpkg -i 軟體套件名.deb
卸載.deb檔案，可以使用Adept，或輸入：
sudo apt-get remove 軟體套件名稱
```

用 dpkg 安裝沒有想到可以這樣做

但有看到一種方式

```bash
sudo apt install ./anydesk_4.0.1-1_amd64.deb
```

[Command Line Interface - AnyDesk Help Center](https://support.anydesk.com/Command_Line_Interface)
安裝完設定，目前不確定是否要執行`--start-service`

```
anydesk --start-service
anydesk --get-status
```

可以開啟`sudo anydesk`設定一些東西
但是設定`固定密碼`一直失敗

```bash
sudo anydesk-global-settings
```

要打上面指令才能設定
