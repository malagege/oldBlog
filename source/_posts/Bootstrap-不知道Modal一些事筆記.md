---
title: Bootstrap 不知道Modal一些事筆記
date: 2019-02-19 22:40:37
tags:
categories:
---

說真的平常只用 Boostrap 基本東西
很少深入研究這個東西
今天看到一段程式
感覺 Bootstrap 值得深入研究

<!--more-->

## remote (v4 以廢棄)

```html
<a data-toggle="modal" href="remote.html" data-target="#modal">Click me</a>
```

可以呼叫`remote.html`到`#modal`裡面去
沒錯我當初看到的時候
覺得很神奇
跑去官網文件都沒有找到
結果查看了一下 v3

> This option is deprecated since v3.3.0 and has been removed in v4. We recommend instead using client-side templating or a data binding framework, or calling jQuery.load yourself

這麼好用的功能...，怎麼要廢棄掉???
[Bootstrap 3 with remote Modal - Stack Overflow](https://stackoverflow.com/questions/18378720/bootstrap-3-with-remote-modal)
所以之後可能需要用 jQuery.load 手動載入方式

### 神奇清除 remote 表單

```js
$("#myModal").on("hidden.bs.modal", function() {
  $(this).removeData("bs.modal");
});
```

[Plunker](http://plnkr.co/edit/HWSgSw?p=preview)
其實仔細觀察，開發者工具看，他會重新載入網頁
所以值都是一開始的樣子

有時候還會失敗，最好的方式就是

不過這個方法，只能適用在 remote
所以網路上只看到 v2,v3
但沒有看到 v4

## 修改表單小技巧

[Modal · Bootstrap](https://getbootstrap.com/docs/4.3/components/modal/)

平常我們重開 modal，打開兩次，第一次修改，直接取消，第二次打開後可能是第一次修改取消的值
原本 remote 方式可以輕鬆解決
但是網路上有找到用`data-*`方式去紀錄

[Correct approach of getting dynamic values from data-attributes with jQuery](https://codepen.io/mkrl/pen/ZmWVMJ)

```html
<button
  type="button"
  id="btn1"
  class="btn btn-primary"
  data-toggle="modal"
  data-target="#exampleModal"
  data-title="Post 1"
>
  Edit
</button>

<div
  class="modal fade"
  id="exampleModal"
  tabindex="-1"
  role="dialog"
  aria-labelledby="exampleModalLabel"
  aria-hidden="true"
>
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">New message</h5>
        <button
          type="button"
          class="close"
          data-dismiss="modal"
          aria-label="Close"
        >
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <form>
        <div class="modal-body">
          <div class="form-group">
            <label for="title" class="col-form-label">Title:</label>
            <input type="text" class="form-control" id="title" name="title" />
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-dismiss="modal">
            Close
          </button>
          <button
            type="button"
            class="btn btn-primary"
            onClick="edit(this.form)"
          >
            Submit
          </button>
        </div>
      </form>
    </div>
  </div>
</div>
```

```js
$("#exampleModal").on("show.bs.modal", function(event) {
  var button = $(event.relatedTarget);
  var recipient = button.attr("data-title");
  var modal = $(this);
  modal.find(".modal-body input#title").val(recipient);
});

function edit(form) {
  var title = form.title.value;
  ////// insert into database
  var btn1 = document.getElementById("btn1");
  btn1.setAttribute("data-title", form.title.value);
}
```

但 Modal 還不只這樣
有空深入研究在寫
