---
title: Windows Sandbox 參數調整設定小記
date: 2022-07-31 01:15:56
tags: [windows,sandbox]
categories: Windows
---

有空研究在整理

<!--more-->

## 隨意小記

[分享一組我在啟動 Windows Sandbox 時都會執行的初始設定腳本 | The Will Will Web](https://blog.miniasp.com/post/2022/01/02/Windows-Sandbox-initial-setup-scripts)


[(11) How to configure Windows Sandbox - YouTube](https://www.youtube.com/watch?v=2Vtd12ek9vg)

https://docs.microsoft.com/zh-tw/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7.2

powershell 呼叫
https://stackoverflow.com/questions/64795150/windows-sandbox-powershell-logon-command-window-not-visible


```xml=
<Configuration>
  <MappedFolders>
    <MappedFolder>
      <HostFolder>C:\SandboxScripts</HostFolder>
      <ReadOnly>true</ReadOnly>
    </MappedFolder>
    <MappedFolder>
      <HostFolder>C:\CodingProjects</HostFolder>
      <ReadOnly>false</ReadOnly>
    </MappedFolder>
  </MappedFolders>
  <LogonCommand>
    <Command>powershell -executionpolicy unrestricted -command "start powershell {-noexit -file C:\SandboxScripts\autorun.ps1}"</Command>
  </LogonCommand>
</Configuration>
```