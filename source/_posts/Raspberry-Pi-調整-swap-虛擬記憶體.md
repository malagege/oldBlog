---
title: Raspberry Pi 調整 swap 虛擬記憶體
date: 2021-04-04 15:56:05
tags: [linux, swap]
categories: Linux
---

最近用 prometheus 設定兩台樹梅派
Swap 吃到99%~100%
樹梅派預設都是設定100MB
目前曾經 transmission 有因為記憶體不足關係
有中斷過...，因為記憶體關係

<!--more-->

調大 SWAP 參考[樹莓派 Raspberry Pi 記憶體不足當機？調整 Swap 交換空間大小解決 - G. T. Wang](https://blog.gtwang.org/iot/raspberry-pi/raspberry-pi-swap-configuration-using-usb-stick/)

```bash
sudo vim /etc/dphys-swapfile

# 100MB設定注解
# 設定 CONF_SWAPFACTOR=2
sudo service dphys-swapfile restart
```

完成
再觀查這幾個還會不會吃到快滿狀況