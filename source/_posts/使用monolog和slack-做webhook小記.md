---
title: 使用monolog和slack 做webhook小記
date: 2019-03-19 20:03:39
tags: [monolog, php, slack]
categories: PHP
---

最近在看 monolog 程式如何使用
不過這篇不會詳細記錄 monolog
這篇主要是記錄 slack webhook
怕之後會忘記要怎麼串接 webhook

<!--more-->

雖然我平常沒有什麼在玩 slack
同事有開一個 slack，但沒有什麼人再用
monolog 內建有 slack

slack 我有 google 到一篇說可以用三種方式連線
[嘗試利用 slack api 發送訊息到 teams 內 – taihua – Medium](https://medium.com/@taihua/slack-api-%E5%98%97%E8%A9%A6%E7%99%BC%E9%80%81%E8%A8%8A%E6%81%AF%E5%88%B0team%E5%85%A7-6d88070cacd5)
今天簡單使用 webhook 方式連

首先，從這邊登入[Slack API: Applications | Slack](https://api.slack.com/apps?new_app=1)
可以`create app`

{% asset_img 1.png "create app" %}
{% asset_img 2.png "add webhook" %}
點選 on，下面`add webhook ...`按鈕點下去
會跳出授權頁面，這時候你只要選擇頻道或使用者
會得到一串網址

接下來 monolog 使用 slack webhook
簡單使用如下
就只要把 webhook 網址帶進去就可以用了(太簡單了)

```php main.php
<?php
require_once './vendor/autoload.php';

use Monolog\Logger;
use Monolog\Handler\StreamHandler;
use Monolog\Handler\SlackWebhookHandler;

// create a log channel
$log = new Logger('name');
$log->pushHandler(new StreamHandler('path/to/your.log', Logger::WARNING));
$log->pushHandler(new SlackWebhookHandler('https://hooks.slack.com/services/xxxxx'));

// add records to the log
$log->warning('Foo');
$log->crit('中文測試');
```

{% asset_img 3.png 成果圖 %}

這邊還需要注意一件事情
`$log->pushHandler(new SlackWebhookHandler('https://hooks.slack.com/services/xxxxx'));`
這邊沒有指定 level 等級
我發現 monolog 原生物件 hanlder 每一個預設 level 都不太一樣
以這邊 slack 為例，他預設是`$level = Logger::CRITICAL`
PS 所以上方的 warning 程式不會在 slack 出現
不知道的話可以直接看原始碼比較快

[The PHP Package 之 monolog 不完全但够用指南 - 后端 - 掘金](https://juejin.im/entry/574f072d49830c00584680de)
[错误与日志 | 《Laravel 5.4 中文文档》 | Laravel China 社区 - 高品质的 Laravel 开发者社区](https://learnku.com/docs/laravel/5.4/errors/1249)

> 一旦配置了此选项，Laravel 将记录大于或等于指定日志等级的所有级别。例如，默认将 log_level 设置为 error 那么将会记录 error , critical , alert 和 emergency 日志信息。
> Monolog 识别以下日志等级 - 从低到高为: debug , info , notice , warning , error , critical , alert , emergency。

這邊先小記，哈哈，不知道之後有沒有有空再寫一篇

話說 flexget 好像也能做 slack,discord
剛好最近有用 discord
有空研究看看

2019-03-20

今天剛好看到 github 有 discord 的 webhook 現成工具
[lefuturiste/monolog-discord-handler: A simple discord handler for support Discord webhooks](https://github.com/lefuturiste/monolog-discord-handler)
原本要找 webhook
進去 discord 開發者頁面找不到 webhook
後來 google 一下
原來可以到`頻道設定`裡面有 webhook
輕鬆就能新增 webhook

{% asset_img 4.png '頻道設定1' %}
{% asset_img 5.png '頻道設定2' %}

程式碼

```php discord.php
<?php
require_once './vendor/autoload.php';

use Monolog\Logger;

$log = new Monolog\Logger('your name');

$log->pushHandler(new DiscordHandler\DiscordHandler([
'https://discordapp.com/api/webhooks/xxxxxxxxx/ooooooooooooooooooooooooooooooooooooooooo'
], 'name', 'subname', 'DEBUG'));


// add records to the log
$log->warning('Foo');
$log->crit('中文測試');
```

{% asset_img 6.png 成果 %}
