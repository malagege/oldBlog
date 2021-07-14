---
title: Vue 使用綁定 class  筆記
date: 2021-07-14 20:47:20
tags: [vue]
categories: Vue
---


在使用 Bootstrap 會用到 Navbar 選單，做到動態選單時候會用到 isActive，要怎麼透過 Vue 綁定到 class 上面?照官方實作可以簡單完成。


<!--more-->

## 需求解法

```htmlembedded=
                <li class="nav-item">
                  <a class="nav-link" :class="{active: mode === 'edit'}" aria-current="page" href="javascript:;" @click="mode = 'edit'">編輯</a>
                </li>
                <li class="nav-item">
                  <a class="nav-link" :class="{active: mode === 'result'}" href="javascript:;" @click="mode = 'result'">結果</a>
                </li>
```

```javascript=
export default {
  data(){
    return {
      mode: 'edit',
    }
  },
```

## 綁定 HTML Class


### 物件語法

```htmlembedded=
<div :class="{ active: isActive }"></div>
```

## 靜態與動態 Class 結合

```htmlembedded=
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>

```

```javascript=
data() {
  return {
    isActive: true,
    hasError: false
  }
}

```

結果

```htmlembedded=
<div class="static active"></div>
```

### 搭配 computed

```htmlembedded=
<div :class="classObject"></div>
```


```javascript=
data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}

```

## 陣列語法

```htmlembedded=
<div :class="[activeClass, errorClass]"></div>

```

```javascript=
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}

```

result: 
```javascript=
<div class="active text-danger"></div>
```

## 在組件上使用

![](https://i.imgur.com/3H4h4Pn.png)

還滿特別的，建議去看官方文件範例:[Class 与 Style 绑定 - 在組件上使用 | Vue.js](https://v3.cn.vuejs.org/guide/class-and-style.html#%E5%9C%A8%E7%BB%84%E4%BB%B6%E4%B8%8A%E4%BD%BF%E7%94%A8)