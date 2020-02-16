---
title: 'VScode,Sublime 使用套件整理'
date: 2020-02-09 22:42:22
tags: [vscode, sublime]
categories: 實用工具
---

整理使用過

<!--more-->

計畫會依照一年去做

> UNIX:
> 
> code --list-extensions | xargs -L 1 echo code --install-extension
>
> Windows (PowerShell, e. g. using VSCode's integrated Terminal):
> 
> code --list-extensions | % { "code --install-extension $_" }

[How can you export VS Code extension list - Stack Overflow](https://stackoverflow.com/questions/35773299/how-can-you-export-vs-code-extension-list)

Sublime 查詢 package list方法

preferences->Browser Package

ls 列出來

# VScode 和 sublime 套件

## 2020

### VScode

#### 公司

code --install-extension alexdima.copy-relative-path
code --install-extension beaugust.blamer-vs
code --install-extension bierner.emojisense
code --install-extension bierner.markdown-checkbox
code --install-extension bierner.markdown-emoji
code --install-extension bierner.markdown-footnotes
code --install-extension bierner.markdown-image-size
code --install-extension bierner.markdown-preview-github-styles
code --install-extension bmewburn.vscode-intelephense-client
code --install-extension christian-kohler.path-intellisense
code --install-extension dbaeumer.vscode-eslint
code --install-extension donjayamanne.githistory
code --install-extension DotJoshJohnson.xml
code --install-extension dushaobindoudou.translation
code --install-extension eamodio.gitlens
code --install-extension EditorConfig.EditorConfig
code --install-extension eg2.tslint
code --install-extension espresso3389.unixtime-converter
code --install-extension felixfbecker.php-intellisense
code --install-extension formulahendry.auto-close-tag
code --install-extension formulahendry.auto-rename-tag
code --install-extension formulahendry.code-runner
code --install-extension fskorzec.shadow-align
code --install-extension GrapeCity.gc-excelviewer
code --install-extension HackMD.vscode-hackmd
code --install-extension huizhou.githd
code --install-extension humao.rest-client
code --install-extension imperez.smarty
code --install-extension JakeWilson.vscode-cdnjs
code --install-extension janjoerke.align-by-regex
code --install-extension johnstoncode.svn-scm
code --install-extension Kelvin.vscode-sshfs
code --install-extension kokororin.vscode-phpfmt
code --install-extension liximomo.sftp
code --install-extension mhutchie.git-graph
code --install-extension michelemelluso.gitignore
code --install-extension mikestead.dotenv
code --install-extension mkloubert.vscode-remote-workspace
code --install-extension ms-azuretools.vscode-docker
code --install-extension MS-CEINTL.vscode-language-pack-zh-hant
code --install-extension ms-vscode.Go
code --install-extension ms-vscode.sublime-keybindings
code --install-extension mtxr.sqltools
code --install-extension neilbrayfield.php-docblocker
code --install-extension octref.vetur
code --install-extension PKief.material-icon-theme
code --install-extension quicktype.quicktype
code --install-extension redhat.vscode-xml
code --install-extension ryu1kn.partial-diff
code --install-extension sadeghpm.sql-snippets
code --install-extension shd101wyy.markdown-preview-enhanced
code --install-extension shengchen.vscode-leetcode
code --install-extension small.php-ci
code --install-extension softwaredotcom.swdc-vscode
code --install-extension streetsidesoftware.code-spell-checker
code --install-extension teamchilla.blueprint
code --install-extension Tobermory.es6-string-html
code --install-extension tomoki1207.pdf
code --install-extension wwm.better-align
code --install-extension Yukai.vscode-ptt
code --install-extension zhuangtongfa.material-theme


#### 家裡

code --install-extension alefragnani.project-manager
code --install-extension bierner.emojisense
code --install-extension bierner.markdown-checkbox
code --install-extension bierner.markdown-emoji
code --install-extension bierner.markdown-footnotes
code --install-extension bierner.markdown-image-size
code --install-extension bierner.markdown-preview-github-styles
code --install-extension christian-kohler.path-intellisense
code --install-extension codingyu.laravel-goto-view
code --install-extension donjayamanne.githistory
code --install-extension eamodio.gitlens
code --install-extension esbenp.prettier-vscode
code --install-extension felixfbecker.php-intellisense
code --install-extension giladgray.theme-blueprint
code --install-extension Gruntfuggly.todo-tree
code --install-extension HackMD.vscode-hackmd
code --install-extension humao.rest-client
code --install-extension JakeWilson.vscode-cdnjs
code --install-extension jkjustjoshing.vscode-text-pastry
code --install-extension Kelvin.vscode-sshfs
code --install-extension kokororin.vscode-phpfmt
code --install-extension lannonbr.vscode-js-annotations
code --install-extension lukazakrajsek.scss-refactoring
code --install-extension MehediDracula.php-namespace-resolver
code --install-extension michelemelluso.gitignore
code --install-extension mikestead.dotenv
code --install-extension mitchdenny.ecdc
code --install-extension mrmlnc.vscode-scss
code --install-extension MS-CEINTL.vscode-language-pack-zh-hant
code --install-extension ms-vscode.Go
code --install-extension ms-vscode.sublime-keybindings
code --install-extension negokaz.live-server-preview
code --install-extension octref.vetur
code --install-extension oderwat.indent-rainbow
code --install-extension onecentlin.laravel-blade
code --install-extension pranaygp.vscode-css-peek
code --install-extension renesackers.angelscript
code --install-extension ritwickdey.live-sass
code --install-extension ritwickdey.LiveServer
code --install-extension robinbentley.sass-indented
code --install-extension shyykoserhiy.git-autoconfig
code --install-extension small.php-ci
code --install-extension streetsidesoftware.code-spell-checker
code --install-extension Tobermory.es6-string-html
code --install-extension vector-of-bool.gitflow
code --install-extension vscode-icons-team.vscode-icons
code --install-extension yuichinukiyama.vscode-preview-server
code --install-extension zhangciwu.swig-tpl

### Sublime

#### 家裡

 0_package_control_loader.sublime-package
 AdvancedNewFile.sublime-package
 Alignment.sublime-package
'All Autocomplete.sublime-package'
'Bootstrap 3 Snippets.sublime-package'
 BracketHighlighter.sublime-package
'CodeIgniter Snippets.sublime-package'
'CodeIgniter Utilities.sublime-package'
 DocBlockr.sublime-package
 Emmet.sublime-package
 FakeDataGenerator.sublime-package
 GhostText.sublime-package
 Goto-CSS-Declaration.sublime-package
 jQuery.sublime-package
'Package Control.sublime-package'
'PHP Getters and Setters.sublime-package'
'Pretty JSON.sublime-package'
 PyV8/
 Sass.sublime-package
 SqlBeautifier.sublime-package
 StringEncode.sublime-package
'Text Pastry.sublime-package'
'Vuejs Snippets.sublime-package'


#### 公司

'Pretty JSON'/
SFTP/
'0Sublimerge 3'/
'Theme - Default'/
User/