---
title: Codeigniter 安全性小記
date: 2019-06-01 22:06:43
tags: [codeigniter, php]
categories: Codeigniter
---

最近專案由於有 QA 團隊檢查安全性
所以這邊紀錄一下最近專案用 Codeigniter
上一篇有紀錄到一些安全性 [CSRF , SSRF , XSS 有關網頁安全小記 | 程式狂想筆記](https://malagege.github.io/blog/2019/05/26/CSRF-SSRF-XSS%E6%9C%89%E9%97%9C%E7%B6%B2%E9%A0%81%E5%AE%89%E5%85%A8%E5%B0%8F%E8%A8%98/)

<!--more-->

## SQL 需要注意安全性問題

首先，講一下 SQL INJECTION
假如要放 SQL 進去查詢，但是複雜的功能平常不可能用 bind 用 `?` 去套
所以再用 變數串 SQL 的時候可能就會有危險的問題，但是不能用 bind

這時候需要加 `escape` 或 `escape_str`
原始碼可以參考這個[CodeIgniter/DB_driver.php at 7ec458b45316b9f270e1e03de498244f71c605ea · bcit-ci/CodeIgniter](https://github.com/bcit-ci/CodeIgniter/blob/7ec458b45316b9f270e1e03de498244f71c605ea/system/database/DB_driver.php#L1037)
`$str = array_map(array(&$this, 'escape'), $str);`
PHP 的 array function 的運用真的 千變萬化

**首先講一下 escape 的小雷**
看了一下 Code
想說這兩個功能都一樣
不過`escape`接到 string 型態
外面會自動加`'`單引號
雖然不知道為什麼
所以為什麼`limit ?`不能用 bind
原因可能是 escape 會有問題(會變成 limit '2')
所以在用的時候需要小心

## XSS 小心

常常在網路上看到，通常都是用 JS 攻擊
所以需要做 XSS 過濾
當然輸入要過濾<`script`>
輸出的是要做`htmlspecialchar`
Codeigniter 沒有現代化框架好用東西，如`{{ var_str }}`
但是還是有好用的工具，如 html_escape
相關程式原碼 [CodeIgniter/Common.php at 07cb9fd45f94d928c0f6dcf73f702808e1e03f62 · bcit-ci/CodeIgniter](https://github.com/bcit-ci/CodeIgniter/blob/07cb9fd45f94d928c0f6dcf73f702808e1e03f62/system/core/Common.php#L748)
使用上除了可以塞 string 外 他還可以塞 array
會把所有內容做過濾

`xss_clean()`
可以過濾`<script>`
但是注意！！這個不是在過濾 HTML tag(ex: <`img`>,<`a`>)

`$data = $this->security->xss_clean($data);`

當然 img 也會有這個問題
如這篇 [CheatSheetSeries/Cross_Site_Scripting_Prevention_Cheat_Sheet.md at master · OWASP/CheatSheetSeries](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)

### 圖片也要注意 XSS 攻擊

```php
if ($this->security->xss_clean($file, TRUE) === FALSE)
{
        // file failed the XSS test
}
```

```php
$this->input->get(xxx , TRUE );
```

## SSRF 安全過濾

在寫 call curl API 需要安全行確認
是不是參數是安全的
例如

```php
<?php
function callAPI($url){
    ...
    curl($url)
}
```

上面 code 不能跑
這就會有 SSRF 問題
聽說 Server 端也能處理這個問題

[CodeIgniter 的数据安全过滤全解析 - Just Code - ITeye 博客](https://justcoding.iteye.com/blog/546880)
[深入探討 CodeIgniter Input Class 核心程式流程 | 小惡魔 - 電腦技術 - 工作筆記 - AppleBOY](https://blog.wu-boy.com/2011/08/%E6%B7%B1%E5%85%A5%E6%8E%A2%E8%A8%8E-codeigniter-input-class-%E6%A0%B8%E5%BF%83%E7%A8%8B%E5%BC%8F%E6%B5%81%E7%A8%8B/)
