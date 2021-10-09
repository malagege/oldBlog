---
title: Vue3 v-model 語法糖
date: 2021-10-09 23:22:02
tags: [vue,v-model]
categories: Vue
---

之前在學 Vue 2 的時候，稍微釐清 v-model 觀念。但一陣子沒用了，忘了差不多，今天在讀 Vue3 順便來回顧一下之前觀念，順便來筆記一下。

<!--more-->

## 一般 Component 

```htmlembedded=
<ChildComponent v-model="pageTitle" />

<!-- 簡寫: -->

<ChildComponent
  :modelValue="pageTitle"
  @update:modelValue="pageTitle = $event"
```

### 重點1(簡寫)

簡單說明
v-model 簡寫 => `:modelValue="xxx" @update:modelValue="xxxx"` 
現在 Vue 3 也可以控制 v-model而外變數
`v-model:title` => `:title="xxx" @update:title="xxxx"`

### 重點2(觸發 Event 改變 ModelValue 值)

這邊 `@update:modelValue="xxx"`和`@update:title="xxxx"` 這邊需要知道 Vue 處理 Event 方式，內層 Component 使用 `this.$emit('update:modelValue',changeValue)`去做 Event 觸發動作

## Form  ModelValue一些方式

我們前一篇講到 Component 簡寫，但在 Form(input , textarea ...)就不是這樣了。
自己有在官方文件看到寫法。

```
<input v-model="searchText" />

<!-- 簡寫 --> 

<input :value="searchText" @input="searchText = $event.target.value" />

```

[Form Input Bindings | Vue.js](https://v3.vuejs.org/guide/forms.html#modifiers)

### select

一般 select 寫法

```
<div id="v-model-select-dynamic" class="demo">
  <select v-model="selected">
    <option v-for="option in options" :value="option.value">
      {{ option.text }}
    </option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>

Vue.createApp({
  data() {
    return {
      selected: 'A',
      options: [
        { text: 'One', value: 'A' },
        { text: 'Two', value: 'B' },
        { text: 'Three', value: 'C' }
      ]
    }
  }
}).mount('#v-model-select-dynamic')

```


### checkbox

```
<!-- `picked` is a string "a" when checked -->
<input type="radio" v-model="picked" value="a" />

<!-- `toggle` is either true or false -->
<input type="checkbox" v-model="toggle" />

<!-- `selected` is a string "abc" when the first option is selected -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>

```

#### true-value,false-value

```htmlembedded=
<input type="checkbox" v-model="toggle" true-value="yes" false-value="no" />

```

true ==> 'yes'
false ==> 'false'


#### 補充 v-model 語法糖寫法

```htmlembedded=
Vue.component('checkbox', {
    model: {
      prop: 'checked',
      event: 'change'
    },
    props: {
      checked: {
        type: Boolean,
        default: false
      }
    },
    template:
          `
      <input type="checkbox" @input="onInput" @change="onChange"/>
    `,
    methods: {
      onInput (e) {
        console.log('onInput', e)
      },
      onChange (e) {
        console.log('onChange', e)
        this.$emit('change', e.target.checked)
      }
    }
  })
```

注意這邊不是用`event.target.value`，是用`event.target.checked`。這個問題讓我卡了幾小時的bug 😢😢😢

參考來源:
- [Vue v-model语法糖 - 知乎](https://zhuanlan.zhihu.com/p/107454413)
- [Vue.js Core 30天屠龍記(第26天): 客製事件 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10209183)
### Radio

```htmlembedded=
<input type="radio" v-model="pick" v-bind:value="a" />

// when checked:
vm.pick === vm.a

```

### select option

```htmlembedded=
<select v-model="selected">
  <!-- inline object literal -->
  <option :value="{ number: 123 }">123</option>
</select>

// when selected:
typeof vm.selected // => 'object'
vm.selected.number // => 123

```


### 修飾詞(這邊都是 form 相關 tag 限定)

1. lazy  change event 才做改變值動作
2. number

```htmlembedded=
 <input type="number" v-model.number="xxx" />
```

這邊注意，type沒限定一定要用 number，這邊指令`text`的話，純文字(沒打英文)會顯示字串。所以搭配 type 還是最好用 `number`做限定。

4. trim

只能用在 v-model



[Components Basics | Vue.js](https://v3.vuejs.org/guide/component-basics.html#emitting-a-value-with-an-event)

## 相關文章


[v-model | Vue3中文文档 - vuejs](https://vue3js.cn/docs/zh/guide/migration/v-model.html#%E4%BD%BF%E7%94%A8-v-bind-sync)

[自定义事件 | Vue3中文文档 - vuejs](https://vue3js.cn/docs/zh/guide/component-custom-events.html#%E5%A4%9A%E4%B8%AA-v-model-%E7%BB%91%E5%AE%9A)

[Vue v-model语法糖 - 知乎](https://zhuanlan.zhihu.com/p/107454413) 

[v-model之vue2与3的差别 - 知乎](https://zhuanlan.zhihu.com/p/354190874)