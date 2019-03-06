---
title: ElementaryOS設定停用休眠但還是會休眠問題解決方案
date: 2019-02-03 00:23:49
tags:
categories:
---

最近重買 SSD 重灌 ElentmaryOS 0.5 版
發現設定 睡眠 停用
電腦竟然還是會做睡眠
起初我以為是設定的問題
但我看我設定都關掉了
這邊有找到解決方法

<!--more-->

## 解決 elementary os 休眠問題

首先，有在[elementary OS SSD 最佳化 – 皇家工程師](https://royalprogrammer.wordpress.com/2018/01/30/ssd_optimization/)看到

```
關閉休眠 (Hibernation)

elementary OS 的休眠預設是關閉的。

檢查休眠是否為關閉的方式如下：

在終端機執行下列指令使用 nano 編輯器打開 /var/lib/polkit-1/localauthority/10-vendor.d/com.ubuntu.desktop.pkla

sudo nano /var/lib/polkit-1/localauthority/10-vendor.d/com.ubuntu.desktop.pkla

[Disable hibernate by default in upower] 和 [Disable hibernate by default in logind] 下的 ResultActive 為 No 的話就代表休眠是關閉的。如果是 Yes 的話，可以自己改成 No 關閉休眠。
```

目前設定確實跟預設一模一樣
不過睡眠(sleep mode)跟休眠(Hibernation)不太一樣
可參考[[教學] 關機、待命、睡眠、休眠和交互式睡眠的分別 | FoolEgg.com](https://www.foolegg.com/what-are-the-differences-between-shut-down-standby-sleep-hibernate-and-hybrid-sleep/)

最後找到[settings - Elementary OS ignoring suspend disable - elementary OS Stack Exchange](https://elementaryos.stackexchange.com/questions/16519/elementary-os-ignoring-suspend-disable)
裡面[Never suspend when plugged in setting ignored (always uses battery setting for suspend) · Issue #89 · elementary/switchboard-plug-power](https://github.com/elementary/switchboard-plug-power/issues/89)有一個 Github Issue
裡面還是 open
看到有一個 workaround 方式

```
    sudo su

enter, then your password

    su - -s /bin/bash lightdm

enter

    dbus-launch gsettings set org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type nothing

enter

    gsettings get org.gnome.settings-daemon.plugins.power sleep-inactive-ac-type

enter

it should return 'nothing'
```

最後使用真的有效!!!

## 安裝 elementaryOS 0.5 小記

- 安裝 zplug 要裝 git 跟
- 安裝 hack font 可以使用`sudo apt-get install fonts-hack`
- picture-in-picture 使用方法是`win+f`[Picture to Picture Mode in elementary OS 5.0 Juno - YouTube](https://www.youtube.com/watch?v=6FbQzF6h8DA)
  很羨慕嗎?其實 Chrome 內建這個功能
  Windows 也可以用
  只可惜沒有快捷鍵可以做隱藏
  偶爾使用會遇到 bug，但目前無傷大雅

[程序员的七种武器之 Zsh - 从 Hello World 开始](https://dingmingxin.github.io/blog/2016/05/05/%E7%A8%8B%E5%BA%8F%E5%91%98%E7%9A%84%E4%B8%83%E7%A7%8D%E6%AD%A6%E5%99%A8%E4%B9%8Bzsh/#org2976e1f)
