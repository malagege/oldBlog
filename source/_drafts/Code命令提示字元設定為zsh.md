---
title: Code命令提示字元設定為zsh
comments: true
categories: 實用工具
tags: [zsh,visual studio code,]
---

這篇記錄我在code設定command line設定
<!--more-->

# Visual Studio Code設定

```json
{
    "terminal.integrated.shell.windows": "C:\\Users\\Administrator\\.babun\\cygwin\\bin\\zsh.exe",
    "terminal.integrated.fontFamily": "Hack",

}
```

但是後來決定放棄使用這個
{% asset_img vs_zsh.gif code在zsh文字游標怪怪的 %}

打算使用cmder...，不過在[VSCode 使用設定 | Mo1cibo's Blog](https://mousyball.github.io/2018/02/12/my-vscode-setting/#Integrated-Terminal)
有看到好像也是跟我一樣的問題


後來決定使用cmder，不過發現`cmder.exe`快捷鍵`ctrl+‵`可以彈出，再按一次`ctrl+‵`
可以再縮小

{% asset_img cmder_ctrl_.gif code在zsh文字游標怪怪的 %}

感覺也沒必要再設定.....

不過還是紀錄爬文設定
```json
// Command Prompt
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\cmd.exe"
// PowerShell
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe"
// Git Bash
"terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe"
// Bash on Ubuntu (on Windows)
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\bash.exe"

// zsh
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\bash.exe",
"terminal.integrated.shellArgs.windows": [
    "-c",
    "zsh"
],

// cmder
"terminal.integrated.shell.windows": "C:\\Windows\\System32\\cmd.exe",
"terminal.integrated.shellArgs.windows": [
    "/K",
    "C:\\cmder\\vscode.bat"
],

// font & fontSize
"terminal.integrated.fontFamily": "DejaVu Sans Mono for powerline",
"terminal.integrated.fontSize": 15,
```

發現window可以用Ubuntu來啟動zsh
我是用同事介紹的babun
不過window感覺用cmder還滿順
應該會先使用這個為優先

參考來源:
[VSCode 使用設定 | Mo1cibo's Blog](https://mousyball.github.io/2018/02/12/my-vscode-setting/#Integrated-Terminal)