---
title: JavaScript Object 繼承物件屬性大法
date: 2021-10-09 23:24:36
tags: [javascript,object,assign]
categories: JavaScript
---

有時候自幹套件，使用 Object 去設定會非常簡單，但是會有物件繼承問題。要如何快速解決問題呢?我之前有使用過 `jQuery.extend` 來解決這個問題。但是最近寫 Vue，不太可能使用 Query ，我相信 ES6 應該有新寫法(還真的有)，這邊來整理一下。
<!--more-->

## "jQuery"

```javascript=
var object1 = {
  apple: 0,
  banana: { weight: 52, price: 100 },
  cherry: 97
};
var object2 = {
  banana: { price: 200 },
  durian: 100
};
 
// Merge object2 into object1
$.extend( object1, object2 );
// {"apple":0,"banana":{"weight":52,"price":200},"cherry":97,"durian":100}

```

[jQuery.extend的用法-黑暗執行緒](https://blog.darkthread.net/blog/jquery-extend/)

[jQuery.extend() | jQuery API Documentation](https://api.jquery.com/jquery.extend/)

以下參考: [You Might Not Need jQuery](https://youmightnotneedjquery.com/#extend)


## ES6

[ECMAScript 6: merging objects via Object.assign()](https://2ality.com/2014/01/object-assign.html)

```javascript=
//Object.assign()
Object.assign(target, source_1, ..., source_n)
```

## 自幹大法

```javascript=
var extend = function(out) {
  out = out || {};

  for (var i = 1; i < arguments.length; i++) {
    if (!arguments[i])
      continue;

    for (var key in arguments[i]) {
      if (arguments[i].hasOwnProperty(key))
        out[key] = arguments[i][key];
    }
  }

  return out;
};

extend({}, objA, objB);


```


## vue 應用

```htmlembedded=
<input type="number" 
    :value="settings.autotime"
    @change="$emit('update:settings',{ autotime: $event.target.value})"
>秒
```

```javascript=
    updateSettings(settings){
      console.log('updateSettings', settings)
      if(settings.hasOwnProperty('windowAlwaysOnTop')){
        if(settings.windowAlwaysOnTop === true){
          this.enableWindowAlwaysOnTop()
        }else{
          this.diseableWindowAlwaysOnTop()
        }
      }
      const {windowAlwaysOnTop} = settings
      console.log('windowAlwaysOnTop',windowAlwaysOnTop)
      settings.windowAlwaysOnTop = windowAlwaysOnTop
    }
  },
```

但這樣寫真的太麻煩了，每個屬性都要而外自己判斷，這時候我有想到 extend 類似特性可以用到。

```javascript=
    updateSettings(settings){
      console.log('updateSettings', settings)

      Object.assign(this.settings,settings)
    }
  },
  watch:{
    "settings.windowAlwaysOnTop"(newvalue,oldvalue){
      console.log('watch settings.windowAlwaysOnTop',newvalue,oldvalue)
      console.log(this)
      if(newvalue){
        this.enableWindowAlwaysOnTop()
      }else{
        this.diseableWindowAlwaysOnTop()
      }
    }
  },
```

程式碼可以更精簡設定。


## JSON.parse(JSON.stringify(xxxx))

```javascript=
JSON.parse(JSON.stringify(xxxx))
```