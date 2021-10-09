---
title: monaco-editor 使用小記(搭配 Vite )
date: 2021-10-09 22:51:16
tags: [monaco,editor]
categories: JavaScript
---

使用現成 monaco-editor 編輯器，是微軟開源項目，跟 VScode 介面一模一樣。
這邊簡單做個小記。

<!--more-->

## 設定

1. 安裝 monaco-editor (可看官往調整版本)

```bash=
npm install monaco-editor@0.25.2
```


2. 設定 HTML , JS

```htmlembedded=
    <div class="container">
      <div class="monaco" ref="monaco"></div>
    </div>
```

```htmlembedded=
<script>
import {editor} from 'monaco-editor'

export default {
  mounted(){
    editor.create(this.$refs.monaco, {
      value: "function hello() {\n\talert('Hello world!');\n}",
      language: "javascript"
    });

  }
}
</script>

<style scoped>
.monaco{
  border: 1px solid black;
  min-height: 800px;
}
.container{
  height: 2/3
}
</style>
```

主要重點 mounted 不是用 created





## 官方範例

想說為什麼官方沒入門手冊，但其實看官方 Playground 範例就夠了。

官方 Playground: [Monaco Editor Playground](https://microsoft.github.io/monaco-editor/playground.html)




##  console 報錯

不知道是不是使用 Vite 關係，Webpack 感覺好像也會遇到一樣問題。雖然不影響結果，不知道哪裡出了甚麼問題，好像是 worker 沒正常運作，但我還是嘗試怎麼解決。

![](https://i.imgur.com/nfCWpRv.png)

相關參考:
- [Error: Unexpected usage (integration with vue) · Issue #1385 · microsoft/monaco-editor · GitHub](https://github.com/microsoft/monaco-editor/issues/1385)
- [webWorker example: Monaco Editor](https://dev.decoupled.com/docs-magic-webWorker-example-monaco
- [在 Vue3 项目中集成 VSCode - 实现 Vite + Vue3 + MonacoEdit](https://juejin.cn/post/6933463466111926286)


### 解決方法

```javascript=
import editorWorker from 'monaco-editor/esm/vs/editor/editor.worker?worker'
import jsonWorker from 'monaco-editor/esm/vs/language/json/json.worker?worker'
import cssWorker from 'monaco-editor/esm/vs/language/css/css.worker?worker'
import htmlWorker from 'monaco-editor/esm/vs/language/html/html.worker?worker'
import tsWorker from 'monaco-editor/esm/vs/language/typescript/ts.worker?worker'

self.MonacoEnvironment = {
  getWorker(_, label) {
    if (label === 'json') {
      return new jsonWorker()
    }
    if (label === 'css' || label === 'scss' || label === 'less') {
      return new cssWorker()
    }
    if (label === 'html' || label === 'handlebars' || label === 'razor') {
      return new htmlWorker()
    }
    if (label === 'typescript' || label === 'javascript') {
      return new tsWorker()
    }
    return new editorWorker()
  }
}
```

參考來源:[Link](https://github.com/vitejs/vite/discussions/1791#discussioncomment-321046)

你會發現 Firefox 不正常，後來爬了一下文[javascript - SyntaxError: import declarations may only appear at top level of a module - Stack Overflow](https://stackoverflow.com/questions/42237388/syntaxerror-import-declarations-may-only-appear-at-top-level-of-a-module)，換成 Chrome 就正常了...。所以這邊還是用 Chrome 開發好了。


GIT: 49fe93f173b92f97904331028b2bc500c9b00368


## manaco-editor 搭 yaml 

```bash=
npm install js-yaml -D
```

以人有用過[寫測試程式用js-yaml對設定檔做簡查 | 程式狂想筆記](https://malagege.github.io/blog/2017/12/13/logdown/2017-12-13-js-yaml-write-test-program-to-do-a-simple-check-on-configuration-files/)，太久之前用的，結果搜尋找到自己的文章XD。

ESM 怎麼載入呢?

```javascript=
import  * as jsYaml from  'js-yaml'
console.log('jsYaml',jsYaml)
```


## manaco-editor 搭 yaml 驗證

使用 monaco-yaml，可以自己訂一個 Yaml Schema Json ，然後定完可用 manaco 做驗證 yaml 是否符合自己訂的內容，我覺得還滿有趣的，但目前不打算深入研究。

參考: 
- [使用 monaco-editor 验证 YAML 数据](http://pengx17.github.io/frontend/2018/09/20/monaco-yaml)
- [pengx17/monaco-yaml: YAML plugin for the Monaco Editor](https://github.com/pengx17/monaco-yaml)


## 類似監聽 change event

```javascript=
    pageEditor.onDidChangeModelContent(e=>{
      console.log(pageEditor.getValue())
      this.test = pageEditor.getValue()
      console.log(jsYaml.load(this.test))
    })
```


## 在webpack中使用monaco-editor


[在webpack中使用monaco-editor - zhangzongshan - 博客園](https://www.cnblogs.com/zzsdream/p/14088741.html)