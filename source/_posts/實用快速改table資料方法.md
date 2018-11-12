---
title: 實用快速改table資料方法
date: 2018-10-20 15:17:45
tags: [html,js]
categories: 網頁
---

最近看到一種很神奇的寫法
在這邊簡單記錄一下


<!--more-->

```html
<table>
    <tr>
        <th></th>
        <th>AA</th>
        <th>BB</th>
        <th>CC</th>
        <th>DD</th>
        <th>EE</th>
    </tr>
    <tr>
        <th>項目1<th>
        <td class="AA">AA</td>
        <td class="BB">BB</td>
        <td class="CC">CC</td>
        <td class="DD">DD</td>
        <td class="EE">EE</td>
    </tr>
    <tr>
        <th>項目2<th>
        <td class="AA">AA</td>
        <td class="BB">BB</td>
        <td class="CC">CC</td>
        <td class="DD">DD</td>
        <td class="EE">EE</td>
    </tr>
    <tr>
        <th>項目2<th>
        <td class="AA">AA</td>
        <td class="BB">BB</td>
        <td class="CC">CC</td>
        <td class="DD">DD</td>
        <td class="EE">EE</td>
    </tr>
</table>
```


```js
    $('table td').click(function(){    
        if(!$(this).is('.input')){    
            $(this).addClass('input').html('<input type="text" value="'+ $(this).text() +'" />').find('input').focus().blur(function(){    
                var id = $(this).parent().siblings("th:eq(0)").text();    
                var thisclass = $(this).parent().attr("class");
                var thisvalue=$(this).val();    
                 
                $.ajax({    
                  type: 'POST',    
                  url: 'update',    
                  data: "id="+id+"&thisclass="+thisclass+"&thisvalue="+thisvalue,
                });    
                $(this).parent().removeClass('input').html($(this).val() || 0);    
            });                        
        }    
    }).hover(function(){    
        $(this).addClass('hover');    
    },function(){    
        $(this).removeClass('hover');    
    });    
```

看到這個寫法，覺得他還缺少了存檔驗證
所以修改程式碼
```js
$('table td').click(function(){    
    var source_text = $(this).text();
if(!$(this).is('.input')){    
    $(this).addClass('input').html('<input type="text" value="'+ $(this).text() +'" />').find('input').focus().blur(function(){ 
        if (source_text == $(this).val()){   
            $(this).parent().removeClass('input').html($(this).val() || 0);    
        }
    }).change(function(){
        if (source_text == $(this).val()) return false;

                var id = $(this).parent().siblings("th:eq(0)").text();    
                var thisclass = $(this).parent().attr("class");
                var thisvalue=$(this).val();    
         
        $.ajax({    
          type: 'POST',    
          url: 'update.php',    
          data: "thisid="+thisid+"&thisclass="+thisclass+"&thisvalue="+thisvalue,
          success:()=>{
            $(this).parent().removeClass('input').html($(this).val() || 0);   
          },
          error:()=>{
            alert('更新失敗');
            $(this).parent().removeClass('input').html(source_text || 0);
          }

        });    
    });                        
}    
}).hover(function(){    
$(this).addClass('hover');    
},function(){    
$(this).removeClass('hover');    
});    
```


PHP部份就不寫了，其實可以不要用`class`來當改db名字
不然還要多處理class 後面加的不份
用`class`還需PHP後端還需要`split`空白過濾之後的東西


PHP回傳錯誤，這樣能讓ajax進入error function
[PHP: http_response_code - Manual](http://php.net/manual/en/function.http-response-code.php)