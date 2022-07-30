---
title: Gitlab Runner 同時跑多台電腦方法
date: 2022-07-31 01:19:38
tags: [gitlab,git,runner]
categories: Git
---

找到相關有兩個方法，參照這篇文章:[如何让多个 gitlab-runner 运行同一个 tag 的 job_已解决_博问_博客园](https://q.cnblogs.com/q/139372/)

目前沒有環境測試，看是觸發只會跑一個tag的Runner。看資料整理出來修改設定，以下試試看。

<!--more-->

## extends(兩台主機，安裝Runner，tag設定不同)

```yaml=
stages:
- deploy-prod-main
- deploy-prod-sub

.deploy-prod:
  msbuild:
    stage: build
    only:
      - main
    script:
      - echo "Stop IIS Pool"
      - $state = (Get-WebAppPoolState -Name MobileAttendanceBackend).Value
      - if($state -Like "Start*") {Stop-WebAppPool -Name MobileAttendanceBackend}
      - echo "Nuget restore"
      - C:\MobileAttendance\Deploy\nuget.exe restore
      - echo "Copy web.config"
      - xcopy C:\MobileAttendance\Deploy\web.config C:\MobileAttendance\Backend\ /y
      - echo "MSBuild"
      - '& "C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin\amd64\msbuild.exe" mobile-attendance-api.sln /p:platform="any cpu" /p:configuration="release" /t:mobile-attendance-api /p:OutDir=C:\MobileAttendance\Backend'
      - echo "Start IIS Pool"
      - C:\Windows\System32\inetsrv\appcmd start apppool /apppool.name:MobileAttendanceBackend

deploy-prod-main:
     extends: .deploy-prod
     tags:
        - prod-main

deploy-prod-sub:
     extends: .deploy-prod
     tags:
        - prod-sub

last-job:
  stage: .post
  tags:
    - "MobileAttendanceVM"
  only:
    - main
  script:
    - echo "All Stages Finished."
```

這邊兩台主機設定不一樣tag，一台`prod-main`另一台`prod-sub`

## parallel(兩台主機安裝Runner，tag設定相同)

```yaml=
stages:
  - build

msbuild:
  stage: build
  tags:
    - "MobileAttendanceVM"
  only:
    - main
  script:
    - echo "Stop IIS Pool"
    - $state = (Get-WebAppPoolState -Name MobileAttendanceBackend).Value
    - if($state -Like "Start*") {Stop-WebAppPool -Name MobileAttendanceBackend}
    - echo "Nuget restore"
    - C:\MobileAttendance\Deploy\nuget.exe restore
    - echo "Copy web.config"
    - xcopy C:\MobileAttendance\Deploy\web.config C:\MobileAttendance\Backend\ /y
    - echo "MSBuild"
    - '& "C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\MSBuild\Current\Bin\amd64\msbuild.exe" mobile-attendance-api.sln /p:platform="any cpu" /p:configuration="release" /t:mobile-attendance-api /p:OutDir=C:\MobileAttendance\Backend'
    - echo "Start IIS Pool"
    - C:\Windows\System32\inetsrv\appcmd start apppool /apppool.name:MobileAttendanceBackend
  variables:
    DEPLOY_RUNNER: ${DEPLOY_RUNNER}
  parallel:
    matrix:
      - DEPLOY_RUNNER: [server1, server2]

last-job:
  stage: .post
  tags:
    - "MobileAttendanceVM"
  only:
    - main
  script:
    - echo "All Stages Finished."
```

兩台主機Runner設定相同Tag，如:`MobileAttendanceVM`。`DEPLOY_RUNNER`看說明是隨意打兩個名稱(不用對照名字)，這邊寫幾個就會執行幾個Runner。


## 其他相關文章

[使用 Gitlab CI/CD 实现自动化发布站点到 IIS - Wigor - 博客园](https://www.cnblogs.com/AMortal/p/10845783.html)

[超简单！asp.net core前后端分离项目使用gitlab-ci持续集成到IIS_mb607022e25a607的技术博客_51CTO博客](https://blog.51cto.com/u_15162069/2756194)

[How to setup Continuous Integration/Deployment with GitLab for ASP NET Core application](https://kimsereyblog.blogspot.com/2017/12/how-to-setup-continuous.html)

[How To Set Up a Continuous Deployment Pipeline with GitLab CI/CD on Ubuntu 18.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-continuous-deployment-pipeline-with-gitlab-ci-cd-on-ubuntu-18-04)

[如何让多个 gitlab-runner 运行同一个 tag 的 job_已解决_博问_博客园](https://q.cnblogs.com/q/139372/)


[Gitlab CI/CD 介紹與 Runner 的架設. 此篇參考[DevOps] Gitlab CI/CD 及 GitLab 的… | by Kiwi lee | Medium](https://sean22492249.medium.com/gitlab-ci-cd-%E4%BB%8B%E7%B4%B9%E8%88%87-runner-%E7%9A%84%E6%9E%B6%E8%A8%AD-afdbde9f22aa)