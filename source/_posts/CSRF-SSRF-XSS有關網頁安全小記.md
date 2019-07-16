---
title: "CSRF , SSRF , XSS 有關網頁安全小記"
date: 2019-05-26 16:24:44
tags: [CSRF, SSRF, XSS]
categories: 程式心法
---

最近看到相關網頁安全東西
XSS,CSRF 我也比較長看到
但是 SSRF 就沒什麼聽過
筆記筆記

<!--more-->

找到資訊

- [【庖丁解牛】CSRF 和 SSRF，是如何攻防？ - 知乎](https://zhuanlan.zhihu.com/p/28657325)
- [XSS,CSRF,SSRF 三种漏洞的区别和共同点 - 18 年 3 月萌新白帽子 - CSDN 博客](https://blog.csdn.net/sdb5858874/article/details/80896784)
- [csrf 以及 ssrf - 简书](https://www.jianshu.com/p/2f60dbd68eb8)
- [SSRF 服务端请求伪造 - CTF Wiki](https://ctf-wiki.github.io/ctf-wiki/web/ssrf/)

  傳接 API 需不需要加入 CSRF 判斷
  所以查了一篇
  好像也沒有一定答案
  但有說串接 API 需要帶 Key

- [请教大家 app 接口需要防护 csrf 吗？ · Ruby China](https://ruby-china.org/topics/32166)

無相關小記

- [WWDC 中提到的浏览器 Fingerprinting 有多可怕？ - 掘金](https://juejin.im/post/5b17de31f265da6e397b70f4)
- [資料庫讀寫分離架構實踐 | 程式前沿](https://codertw.com/%E4%BC%BA%E6%9C%8D%E5%99%A8/165196/)

有時間找到再補充

**2019-07-01**

最近用 CI 做 CSRF
在頁面 form 和 ajax 做 CSRF
PS: `$config['csrf_protection'] = TRUE;` 設定
方法是用`data: {'<?php echo $this->security->get_csrf_token_name(); ?>':'<?php echo $this->security->get_csrf_hash(); ?>'`
但在 SPA 做 CSRF 就會遇到 token 過期
因為 CSRF token 只能用一次
這個一不小心就踩到的問題

[codeigniter 使用 CSRF Token 含 AJAX 驗證 – Bryce'S Note](http://blog.twbryce.com/codeigniter-%E4%BD%BF%E7%94%A8csrf-token/)
網路找幾個方案

1. Cookies 來抓 CSRF，因為 CSRF 是從 cookies 那邊設定

```js
$(function() {
  $("#btn").click(function() {
    $.ajax({
      type: "POST",
      url: "/ajax", //ajax接收的server端
      data:
        $("#form").serialize() +
        "&csrf_test_name=" +
        getCookie("csrf_test_name"),
      success: function(data) {
        alert(data.msg);
      },
      dataType: "json"
    });
  });
});

function getCookie(name) {
  var arr = document.cookie.match(new RegExp("(^| )" + name + "=([^;]*)(;|$)"));
  if (arr != null) return unescape(arr[2]);
  return null;
}
```

[Codeigniter 開啟 csrf protection 時 傳送表單(or ajax) | [lifeIs: tooShort to: waste]](https://blog.hsin.tw/2011/codeigniter-csrf-protection-form-ajax/)

### Laravel AJAX 解法

先說說一般用法
`@csrf`

1.

```js
$.ajaxSetup({
  headers: { "X-CSRF-TOKEN": "{{ csrf_token() }}" }
});
```

2.

```html
<meta name="_token" content="{{ csrf_token() }}" />
```

再用 js

```
$.ajaxSetup({
   headers: {
       'X-CSRF-TOKEN': $('meta[name="_token"]').attr('content')
   }
});
```

3.  從 cookie 那邊得來 CSRF，跟上面 CI 方法很像，異曲同工之妙。

```js
$.ajaxSetup({
  headers: {
    "X-XSRF-TOKEN": $.cookie("XSRF-TOKEN")
  }
});
```

我同事有找到一篇，先打 ajax 取 csrf，在發送表單...
不過我們都覺得不優 XD

PS: Laravel 會把 CSRF 寫在 XSRF-TOKEN

[讓我們來談談 CSRF - Huli's blog](https://blog.huli.tw/2017/03/12/csrf-introduction/) {% asset_link web1.png 備份圖 %}
[Laravel 中 Ajax 调用时的 CSRF 对策 – 目光博客](https://eyehere.net/2015/laravel-ajax-csrf/)
[CSRF 保护 |《Laravel 5.5 中文文档》| Laravel China 社区](https://learnku.com/docs/laravel/5.5/csrf/1295)
[codeigniter 使用 CSRF Token 含 AJAX 驗證 – Bryce'S Note](http://blog.twbryce.com/codeigniter-%E4%BD%BF%E7%94%A8csrf-token/)

## AXIOS 搭配用 Laravel AJAX 免設定

[axios/axios: Promise based HTTP client for the browser and node.js](https://github.com/axios/axios)

```
 // `xsrfCookieName` is the name of the cookie to use as a value for xsrf token
xsrfCookieName: 'XSRF-TOKEN', // default
// `xsrfHeaderName` is the name of the http header that carries the xsrf token value
xsrfHeaderName: 'X-XSRF-TOKEN', // default
```

設定 axios，可以不需要設定 CSRF
前端套件幫你處理了 XD

我覺得有空來實作看看

**2019-07-05**

今天看到

- [IE 的一个通用 Cookie 跨域漏洞 - 知乎](https://zhuanlan.zhihu.com/p/31715835)
- [讓我們來談談 CSRF · Issue #16 · aszx87410/blog](https://github.com/aszx87410/blog/issues/16)
- [twtrubiks/CSRF-tutorial: Use Django To Introduce CSRF and Cookies , Session 📝](https://github.com/twtrubiks/CSRF-tutorial)

講到

- Synchronizer Token Pattern ( STP )
- Double Submit Cookie

> 其中的"某些 CSRF 防禦"特指 Double Cookie Submit。（雙 Cookie 遞交認證，指的是在 GET 參數及 HTTP 請求中同時包含一個隨機的字符串，如果服務器判斷 GET 參數和 Cookie 參數相等，那麼沒有受到 CSRF 攻擊）由於這是 OWASP 欽定的防禦方法，人們往往會忽略 IE Cookie 漏洞對其的繞過。
> [IE 的一個通用 Cookie 跨域漏洞 - 知乎](https://zhuanlan.zhihu.com/p/31715835)

這個寫得很清楚是什麼意思

我覺寫得不錯!!
