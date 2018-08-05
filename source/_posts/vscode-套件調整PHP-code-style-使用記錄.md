---
title: vscode 套件調整PHP code style 使用記錄
date: 2018-07-18 23:34:49
tags: [PHP,vscode,PSR]
categories: 實用工具
---
 * phpfmt
     * [sublime text3 phpfmt插件使用 - eerTime](https://www.eertime.com/archives/86.html)
 * vscode-php-cs-fixer
     * [在 vs code 使用 php cs fixer 進行 coding style 的格式修正 | Codefun](http://codefun.tw/2017/10/23/2017102302-vscode-extension-php-cs-fixer/)
 * php-cs-fixer
     * [使用 PHP-CS-Fixer 自动规范化你的 PHP 代码 - dingdayu的个人空间 - 开源中国](https://my.oschina.net/dingdayu/blog/792497)
 
不知道為什麼裝phpfmt，我vscode不用設定什麼東西就能跑了
後來決定用這個XD
設定方式也很方便

<!--more-->

沒試用的套件
 * sample-vscode-php-cs-fixer
 * PHP Formatter

[vscode 中使用php-cs-fixer和PHP Formatter 插件规范化PHP代码 - HTL - 博客园](https://www.cnblogs.com/huangtailang/p/6604124.html)

可先在作業系統環境path加入php執形資料夾
下載php解壓縮出來，設定作業環境的path


公司PHP設定
* TRUE、FALSE、NULL 要大寫  // psr2檢查要關掉
* if class switch 需要換行  // AllmanStyleBraces
```
  "phpfmt.passes": ["AllmanStyleBraces"],
  "phpfmt.psr2": false,
  "[php]": {
    "editor.formatOnSave": true
  }
```
`"editor.formatOnSave": true`
vscode`檔案編輯頁面`還原git好像會有問題......
但影響不大......
~~這篇好像沒有寫什麼重點   呵呵= =|||~~
