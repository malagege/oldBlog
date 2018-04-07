---
layout: post
title: '超級終端機 fzy'
date: 2018-01-22 02:47
comments: true
categories: Linux
tags: [zplug,Linux,zsh]
---
網路上看到fzy工具
感覺在選擇多個檔案會很有幫助
<!--more-->

```
zplug "jhawthorn/fzy", \
    as:command, \
    rename-to:fzy, \
    hook-build:"
    {
        make
        sudo make install
    } &>/dev/null
    "
    ```

    [![asciicast](https://asciinema.org/a/TNkbpJmduO878wMUsr6iyobxF.png)](https://asciinema.org/a/TNkbpJmduO878wMUsr6iyobxF)