---
title: jQuery ajax的beforeSend妙用
date: 2019-03-05 22:23:51
tags: [jquery, javascript, ajax]
categories: JavaScript
---

最近看到 jQuery 有`beforeSend`
看到這個可以做驗證上面
或著動態載入特效
先來筆記，感覺很容易忘記

<!--more-->

```js
<form method="post" action="test.php">
AA：<input type="text" name="aa" onkeypress="if (event.keyCode == 13) {return false;}">
<input type="radio" name="cc">CC
</form>
```

[XYZ 的筆記本: HTML form 只有一個 text input 時，在 input 上按 enter 會自動送出表單](https://xyz.cinc.biz/2016/07/form-input-enter-auto-submit.html)
之前公司也是用這個解法
但是我覺得這個方法寫在 html 真的很長
有想到一個方法

```js
$("input").on("keypress", e => e.preventDefault());
```

ajax 搭配 submit 事件，實做 ajax 後，下面回傳 false，所以表單不會送出去
`beforeSend`可以做驗證動作
這時候還會有 input 按 Enter 會送出的問題

EX:

```js
$('form').on('submit',function(){

  $.ajax({
    url: '.',
    data:{
      ...
    },
    beforeSend: function(){
      ...
    }
  })
  return false;
});
```

由於暫存筆記有記錄 jquery.form.js
但是一直沒有記錄，趁著這個時間趕快記錄

```js
$("#myForm").ajaxForm(function() {
  $("#output1")
    .html("提交成功！欢迎下次再来！")
    .show();
});

$("#myForm2").submit(function() {
  $(this).ajaxSubmit(function() {
    $("#output2")
      .html("提交成功！欢迎下次再来！")
      .show();
  });
  return false; //阻止表单默认提交
});
```

[淺談 jquery.form.js 的 ajaxSubmit 和 ajaxForm 的使用 | 程式前沿](https://codertw.com/%E5%89%8D%E7%AB%AF%E9%96%8B%E7%99%BC/260779/)

簡單來說`ajaxForm`和`ajaxSubmit`看起來很像
但簡單來說`ajaxForm`是拿當下 form 做 submit 時候，不會送出去，會做 ajax
這就是跟一般 ajax 差別

`ajaxSubmit`是當下呼叫拿當下 form 做 ajax 動作
所以上面範例是寫在`.submit()`裡面

ajax 拿來放在 submit 可以簡單做到表單綁定效果

以下是筆記暫存就放在下面
怕沒有甚麼機會寫到

```
# JQuery Form Validate
[使用jQuery.form庫中ajaxSubmit提交表單時遇到的一些問題 - 掃文資訊](https://hk.saowen.com/a/e36ca7b7b772fcc2699400c165fb9129964364e45217a35412ca0cdb21b37b63)
https://jqueryvalidation.org/documentation/
http://formvalidation.io/examples/

[Bootstrap 表單驗證formValidation 之表單動態驗證 - CSDN博客](https://blog.csdn.net/u012123026/article/details/53288641)
[Bootstrap 表单验证formValidation 之表单动态添加元素之后再次验证 - CSDN博客](https://blog.csdn.net/weixin_40475396/article/details/79879094)


jQuery Form
[jQuery Form Plugin中AjaxForm与AjaxSubmit的区别-飘一代-51CTO博客](http://blog.51cto.com/fluagen/393134)
把傳統表單迅速套成ajaxForm，submit可以做成ajax動作

# waitme

# ajaxsuccess
[Ajax-Global Ajax Event Handlers .ajaxComplete()、.ajaxError()、.ajaxSend()、.ajaxStart()、.ajaxStop()、.ajaxSuccess() | 阿沙布魯 - 點部落](https://dotblogs.com.tw/brooke/2016/07/10/142933)

可搭配waitme實做
```

參考來源:

- [[程式][JQuery] 讓 AJAX 運作的時候，跳出 loding 的訊息。－Part 1 @ 四處流浪的阿基。I am Vagrant Walker :: 痞客邦 ::](https://expect7.pixnet.net/blog/post/39829979-%5B%E7%A8%8B%E5%BC%8F%5D%5Bjquery%5D-%E8%AE%93ajax%E9%81%8B%E4%BD%9C%E7%9A%84%E6%99%82%E5%80%99%EF%BC%8C%E8%B7%B3%E5%87%BAloding%E7%9A%84%E8%A8%8A)
- [jquery-form/form: jQuery Form Plugin](https://github.com/jquery-form/form)
- [jQuery Form Plugin 中 AjaxForm 与 AjaxSubmit 的区别-飘一代-51CTO 博客](https://blog.51cto.com/fluagen/393134)
- [jQuery form 插件的使用--ajaxForm()和 ajaxSubmit()的可选参数项对象 - souvc - 博客园](https://www.cnblogs.com/liuhongfeng/p/5150389.html)
- [防止 ENTER 鍵直接 SUBMIT- 藍色小舖 BlueShop](http://www.blueshop.com.tw/board/FUM20041006152641OLG/BRD20060619104902G2O.html)
- [HTML 防止 input 回车提交表单 - 李刚的学习专栏 - CSDN 博客](https://blog.csdn.net/ligang2585116/article/details/44699567)
- [XYZ 的筆記本: HTML form 只有一個 text input 時，在 input 上按 enter 會自動送出表單](https://xyz.cinc.biz/2016/07/form-input-enter-auto-submit.html)
- [Press Enter to Submit 背后的那些事 « David Chen's Blog](http://david-chen-blog.logdown.com/posts/177766-how-forms-submit-when-pressing-enter)
- [JS Bin - JS Bin](https://jsbin.com/namefaqija/edit?html,css,js,output)
