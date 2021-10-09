---
title: Vite 部屬到 Github Actions 小記
date: 2021-10-09 23:18:01
tags: [vite,github,actions,deploy]
categories: Vite
---

最近研究如何發布或定期執行程式，所以就找了一下並整理。

<!--more-->

## 免費限制

作业执行时间 - 工作流程中的每个作业最多可以运行 6 个小时。 如果作业达到此限制，该作业将会终止而无法完成。

工作流程运行时间 - 每个工作流程的运行时限为 72 小时。 如果工作流程运行时间达到此限制，其运行将被取消。

## 教學

參考: 
https://tzuhui.github.io/2020/12/11/Vue/Vue-deploy-github-actions/


https://milkmidi.medium.com/%E6%B7%B1%E5%85%A5%E4%BD%86%E4%B8%8D%E6%B7%BA%E5%87%BA-%E5%A6%82%E4%BD%95%E7%94%A8-github-actions-%E8%87%AA%E5%8B%95%E7%99%BC%E4%BD%88-gh-pages-8183464dfe84


### 設定 Github Token 給 Github Actions 用

專案設定

![](https://i.imgur.com/Rh5Lgbt.png)


![](https://i.imgur.com/539HkGH.png)

勾選圖片選項
![](https://i.imgur.com/B2Fg3aj.png)

此token 產生之後無法看到，記得先存起來。

### 設定 secret

設定完無法再看到之前設定值，滿特別的東西。

![](https://i.imgur.com/Q0AQgiD.png)



### 前湍 Vite 設定



調整 vite.confing.js


base: '/vite-test-publish-githubpage/',
  
  
### 專案目錄需要設定

.github/workflows/main.yml

```yaml=
# This is a basic workflow to help you get started with Actions
name: deploy

# Controls when the action will run. 
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches:
      - master
  pull_request:
    branches:
      - master
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '0,15,30,45 12,13 * * *'


  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2
      
      - name: Install & Build # 執行指令
        run: |
          npm install
          npm run build 
          
      # Runs a single command using the runners shell
      - name: Deploy
        uses: JamesIves/github-pages-deploy-action@4.1.4
        with:
          # ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }} # Settings > Secret 建立的 ACCESS_TOKEN，推同個 repo 的話可以不需要
          branch: gh-pages # deploy 到 gh-pages 這個分支
          folder: dist # build 後的資料夾
```

有看到文章說`.github/workflows/`會執行當下目錄下 yaml 檔案。


## 排程設定

```yaml=
# Controls when the action will run. 
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches:
      - master
  pull_request:
    branches:
      - master
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '0,15,30,45 12,13 * * *'

```


https://skeptric.com/github-action-cron/

timezone 時區要注意，不是用台灣時間，UTC+0時間。排成也不會馬上啟動，好像大多數排隊消化其他人排成，後續才會輪到，甚至排到兩次可能會取消，請看下面例子。


例如:`cron:  '0,15,30,45 9 * * *'`。
下面都是台灣時間
0   => 
15  => 17:17
30  => 17:39
45  => 17:51

這邊看到 0分時候沒有啟動，原因是 Github Actions 還沒排到，所以15分又啟動排成就不會執行。


## actions-status-discord 通知


```yaml=
      - uses: sarisia/actions-status-discord@v1
        if: always()
        with:
          webhook: ${{ secrets.DISCORD_WEBHOOK }}
          title: "deploy"
          description: "Build and deploy to GitHub Pages"
          username: GitHub Actions
```


## 相關教學文章

https://github.com/JamesIves/github-pages-deploy-action


[史上最齊全最詳細的Drone自動化教學文件 - HackMD](https://hackmd.io/@3obs7uo2R0ywhgjPUysZZg/r1mzSci7w#%E5%BB%BA%E7%AB%8BDrone-Runner-Server%EF%BC%88Agent%EF%BC%89)


[actions/starter-workflows: Accelerating new GitHub Actions workflows](https://github.com/actions/starter-workflows)


https://docs.github.com/cn/actions/reference/events-that-trigger-workflows

https://medium.com/starbugs/%E5%AF%A6%E4%BD%9C%E9%96%8B%E6%BA%90%E5%B0%8F%E5%B7%A5%E5%85%B7-%E8%88%87-github-actions-%E7%9A%84%E7%AC%AC%E4%B8%80%E6%AC%A1%E7%9B%B8%E9%81%87-3dd2d70eeb


[使用drone和gogs搭建自己的CI/CD系统 - SegmentFault 思否](https://segmentfault.com/a/1190000018459195)

[CICD With DroneCI and Gitea Using Docker Compose - Ruan Bekker's Blog](https://blog.ruanbekker.com/blog/2021/03/09/cicd-with-droneci-and-gitea-using-docker-compose/)

## Raspberry PI 架 Drone

[How to set up Drone CI on Raspberry Pi (and why you shouldn't) | Alex Hyett](https://www.alexhyett.com/drone-ci-raspberry-pi/)