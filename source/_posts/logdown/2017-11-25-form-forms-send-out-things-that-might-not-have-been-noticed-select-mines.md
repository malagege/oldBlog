---
layout: post
title: 'Form 送出可能沒注意到的事情(select地雷)'
date: 2017-11-25 13:53
comments: true
categories: HTML
tags: [select,地雷,Form,HTML]
---
因為最近程式需要從IE8到IE11相容易改版
今天同事問我他跟我說他最近網路遇到的怪事
IE8做submit可以接收到多個select資料
但是IE11確不行....
後來發現這真的不是很多人知道的東西
做一下筆記
<!--more-->

#select 地雷

##value設定沒有options裡面的值

當設定select.value='ffff'，但是select沒有內容，select將會為""空字串
```
The value IDL attribute, on getting, must return the value of the first option element in the list of options in tree order that has its selectedness set to true, if any. If there isn't one, then it must return the empty string.
```
[HTML Standard](https://html.spec.whatwg.org/multipage/form-elements.html#dom-select-value)

##select設定沒有內容值，submit不會送出去
**如上面的「value設定沒有options裡面的值」**
不會送出去
可參考第5點
```
If the field element is a select element, then for each option element in the select element's list of options whose selectedness is true and that is not disabled, append an entry to the form data set with the name as the name, the value of the option element as the value, and type as the type.
```
[HTML Standard](https://html.spec.whatwg.org/multipage/form-control-infrastructure.html#concept-form-submit)


##option沒設定value，預設value為text名稱

```html
<select>
    <option>test</option>
</select>
```
上面沒有設定value預設會以text內容為主(上面option value為test)

```
The value attribute provides a value for element. The value of an option element is the value of the value content attribute, if there is one, or, if there is not, the value of the element's text IDL attribute.
```
[HTML Standard](https://html.spec.whatwg.org/multipage/form-elements.html#dom-option-value)


## checkbox radio 地雷
打勾(v)確沒有設定value值，value預設傳on
```
Otherwise, if the field element is an input element whose type attribute is in the Checkbox state or the Radio Button state, then:
    If the field element has a value attribute specified, then let value be the value of that attribute; otherwise, let value be the string "on".
    Append an entry to the form data set with name as the name, value as the value, and type as the type.
```

當沒選擇檔案submit情況
```
Otherwise, if the field element is an input element whose type attribute is in the File Upload state, then for each file selected in the input element, append an entry to the form data set with the name as the name, the file (consisting of the name, the type, and the body) as the value, and type as the type. If there are no selected files, then append an entry to the form data set with the name as the name, the empty string as the value, and application/octet-stream as the type.
```


這有遇到過，但也沒找到相關文件
原來文件有寫這個

#結論
IE8可以跑多個select原因
是因為IE8 bug關係

IE11就照原本W3C文件規範XD
這次真的了解很多東西
