---
title: adminer 管理DB工作裝套件記錄
date: 2018-08-17 22:24:55
tags: [adminer]
categories: PHP
---

這篇記錄adminer要怎麼裝套件，步驟真的不複雜
簡單記一下

<!--more-->

首先我需要一個table欄位置頂功能
我看我plugin都沒有
~~(沒有當然自幹XDD))~~
裝上[stylus]](https://addons.mozilla.org/zh-TW/firefox/addon/styl-us/)

設定
```css
table{
    border-collapse: separate;
}

table td,table th{
    border-color: rgba(153, 153, 153,.7)
}

table thead {
    position: sticky;
    top: 0px;
}
```

其實簡單用到sticky
發現sticky要在table為spearate才有用...
但是sparate真的很難看
原本想用小border
但發現border`沒辦法縮的比1px還小`
[CSS border less than 1px - Stack Overflow](https://stackoverflow.com/questions/13891177/css-border-less-than-1px)

這其實可以獨立寫一篇XD
最後參照stack overflow設定透明度
有看得比較順眼

參考來源
* [CSS border less than 1px - Stack Overflow](https://stackoverflow.com/questions/13891177/css-border-less-than-1px)
* [CSS border-collapse 属性](http://www.w3school.com.cn/cssref/pr_tab_border-collapse.asp)
* [CSS border-spacing 属性](http://www.w3school.com.cn/cssref/pr_tab_border-spacing.asp)
* [css - Why border is not visible with "position: sticky" when background-color exists? - Stack Overflow](https://stackoverflow.com/questions/41882616/why-border-is-not-visible-with-position-sticky-when-background-color-exists)



等等，還沒介紹adminer


可以先新增一個資料夾
把adminer放裡面，再把順眼的adminer plugin抓下來
推薦[struct-comments](https://raw.githubusercontent.com/vrana/adminer/master/plugins/struct-comments.php)
設定一個`index.php`
[使用套件教學](https://www.adminer.org/en/plugins/#use)

```php
<?php
function adminer_object() {
    // required to run any plugin
    include_once "./plugins/plugin.php";
    
    // autoloader
    foreach (glob("plugins/*.php") as $filename) {
        include_once "./$filename";
    }
    
    $plugins = array(
        // specify enabled plugins here
        new AdminerDumpXml,
        new AdminerTinymce,
        new AdminerFileUpload("data/"),
        new AdminerSlugify,
        new AdminerTranslation,
        new AdminerForeignSystem,
    );
    
    /* It is possible to combine customization and plugins:
    class AdminerCustomization extends AdminerPlugin {
    }
    return new AdminerCustomization($plugins);
    */
    
    return new AdminerPlugin($plugins);
}

// include original Adminer or Adminer Editor
include "./adminer.php";
?>
```

[PHP: glob - Manual](http://php.net/manual/en/function.glob.php)
算是很神的method
可以用神奇的方法抓到想要的檔案
自動載入也可以參考__autoload


當然也可以直接去github找人家用好的adminer版本
[dg/adminer-custom: Customizations for Adminer, the best database management tool written in PHP.](https://github.com/dg/adminer-custom)
不過我還是覺得原版看比較順眼
