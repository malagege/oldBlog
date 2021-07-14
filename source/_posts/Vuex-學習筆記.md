---
title: Vuex 學習筆記
date: 2021-06-27 14:48:05
tags: [vue,vuex]
categories: Vue
---

這篇 Vuex 學習筆記，會大量引用[Vuex 官方文章](https://next.vuex.vuejs.org/zh/)，算是自己學習重點筆記。

<!--more-->

## 心法


### 狀態管理模式

```javascript=
const Counter = {
  // 狀態
  data () {
    return {
      count: 0
    }
  },
  // 視圖
  template: `
    <div>{{ count }}</div>
  `,
  // 操作
  methods: {
    increment () {
      this.count++
    }
  }
}

createApp(Counter).mount('#app')

```

以上包含以下幾個部分：

    狀態，驅動應用的數據源；
    視圖，以聲明方式將狀態映射到視圖；
    操作，響應在視圖上的用戶輸入導致的狀態變化。
    
![](https://i.imgur.com/28g38YH.png)

詳細[Vuex 是什么？ | Vuex](https://next.vuex.vuejs.org/zh/)

### vuex

![](https://i.imgur.com/Zg1OK9y.png)


[Vuex 是什么？ | Vuex](https://next.vuex.vuejs.org/zh/)

### 非套件做到的狀態管理

[状态管理 | Vue.js](https://v3.cn.vuejs.org/guide/state-management.html#%E7%BB%99-react-%E5%BC%80%E5%8F%91%E8%80%85%E7%9A%84%E5%8F%82%E8%80%83%E4%BF%A1%E6%81%AF)

## 安裝

```bash=
npm install vuex@next --save
```

###  最簡單的 Store

> Vuex 和單純的全局對象有以下兩點不同：
>
>    Vuex 的狀態存儲是響應式的。當 Vue 組件從 store 中讀取狀態的時候，若 store 中的狀態發生變化，那麼相應的組件也會相應地得到高效更新。
>
>    你不能直接改變 store 中的狀態。改變 store 中的狀態的唯一途徑就是顯式地提交 (commit) mutation。這樣使得我們可以方便地跟蹤每一個狀態的變化，從而讓我們能夠實現一些工具幫助我們更好地瞭解我們的應用。

簡單來講，處理 store 狀態需要提交(commit) mutation 。(所以外部操作 vuex store 可以修改?)

:::success
mutation 翻譯為變化；浮沉盛衰；變質
參考:[mutation - Yahoo奇摩字典 搜尋結果](https://tw.dictionary.search.yahoo.com/search?p=mutation)
:::


一個初始 state 對象和一些 mutation：
這邊還滿有趣的，vuex4 和 vuex3 這邊寫法就不一樣了

Main.js
```javascript=
import { createApp } from 'vue'
import { createStore } from 'vuex'
import App from './App.vue'

// 創建一個新的 store 實例
const store = createStore({
    state () {
      return {
        count: 0
      }
    },
    mutations: {
      increment (state) {
        state.count++
      }
    }
  })
  
// 將 store 實例作為插件安裝
// app.use(store)
createApp(App).use(store).mount('#app')

//測試
store.commit('increment')

console.log(store.state.count) // -> 1


// 證明也是能直接改 state
store.state.count=100
console.log(store.state.count)
```

之前看到舊版本使用 `$this.store`，但看新版本好像很少這樣看到。

> 為了修復 issue #994，Vuex 4 刪除了 this.$store 在 Vue 組件中的全局類型聲明。當使用 TypeScript 時，必須聲明自己的模塊補充(module augmentation)。

可能是因為要讓 TypeScript 支援原因，可參考:[從 3.x 遷移到 4.0 | Vuex](https://next.vuex.vuejs.org/zh/guide/migrating-to-4-0-from-3-x.html#%E5%AE%89%E8%A3%85%E8%BF%87%E7%A8%8B)。


組件呼叫改 State 狀態
```javascript=
methods: {
  increment() {
    this.$store.commit('increment')
    console.log(this.$store.state.count)
  }
}

```

GIT: [https://github.com/malagege/vuex-test/commit/a05d6cc9bd64e11de6be51a57a1f1e547b637a46](https://github.com/malagege/vuex-test/commit/a05d6cc9bd64e11de6be51a57a1f1e547b637a46)


> 再次強調，我們通過提交 mutation 的方式，而非直接改變 store.state.count，是因為我們想要更明確地追蹤到狀態的變化。這個簡單的約定能夠讓你的意圖更加明顯，這樣你在閱讀代碼的時候能更容易地解讀應用內部的狀態改變。此外，這樣也讓我們有機會去實現一些能記錄每次狀態改變，保存狀態快照的調試工具。有了它，我們甚至可以實現如時間穿梭般的調試體驗。
> 
> 由於 store 中的狀態是響應式的，在組件中調用 store 中的狀態簡單到僅需要在計算屬性中返回即可。觸發變化也僅僅是在組件的 methods 中提交 mutation。


這邊修改store.state 需要透過 Vue Component 的 method 去做提交(commit) mutation 。
這邊修改store.state 需要透過 Vue Component 的 method 去做提交(commit) mutation 。
這邊修改store.state 需要透過 Vue Component 的 method 去做提交(commit) mutation 。

因為看起來很重要，所以講三遍XD


## State

> Vuex 使用單一狀態樹——是的，用一個對象就包含了全部的應用層級狀態。至此它便作為一個「唯一數據源 (SSOT )」而存在。


在 Vue 組件中獲得 Vuex 狀態，一般都是用 computed 狀態取得出來。[计算属性和侦听器 — Vue.js](https://cn.vuejs.org/v2/guide/computed.html)

```javascript=
// 創建一個 Counter 組件
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count
    }
  }
}

```

> Vuex 通過 Vue 的插件系統將 store 實例從根組件中「注入」到所有的子組件裡。且子組件能通過 this.$store 訪問到。讓我們更新下 Counter 的實現：
```javascript=
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}

```

###  mapState 輔助函數

小記：通常用在 computed 上面


> 當一個組件需要獲取多個狀態的時候，將這些狀態都聲明為計算屬性會有些重複和冗餘。為瞭解決這個問題，我們可以使用 mapState 輔助函數幫助我們生成計算屬性，讓你少按幾次鍵：

```javascript=
// 在單獨構建的版本中輔助函數為 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // 箭頭函數可使代碼更簡練
    count: state => state.count,

    // 傳字符串參數 'count' 等同於 `state => state.count`
    countAlias: 'count',

    // 為了能夠使用 `this` 獲取局部狀態，必須使用常規函數
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}

```


#### (一般)使用


```javascript=
computed: mapState([
  // 映射 this.count 為 store.state.count
  'count'
])
```

GIT: [https://github.com/malagege/vuex-test/commit/4e9b200cdcca3a043ff9e94c446b2d4900c1e5d0](https://github.com/malagege/vuex-test/commit/4e9b200cdcca3a043ff9e94c446b2d4900c1e5d0)

但一般 `computed` 不可能全部包給 `mapState`。所以我們要可以使用 ES 解構。


```javascript=
computed: {
  localComputed () { /* ... */ },
  // 使用對象展開運算符將此對象混入到外部對象中
  ...mapState({
      count: state=>state.count,
    // ...
  })
}
```

GIT: [https://github.com/malagege/vuex-test/commit/9c690013d6cc52afc1cd72eb801ac16fc4bf5d1d](https://github.com/malagege/vuex-test/commit/9c690013d6cc52afc1cd72eb801ac16fc4bf5d1d)


#### 小記

```
console.log(mapState({count:state=>state.count}))
```
![](https://i.imgur.com/jYqelPi.png)

這邊查看結果，mapState會回傳物件，然後用`...`解構回去塞進 `computed`。

這邊`mapState`有兩個用法。

1. 帶 Array
2. 帶 物件(Object)

Array，只能帶字串，不能做到客製化。

Object ，可做到多個客製化。

這邊第一次看，會有點混亂，分成兩類看就還好。

回顧 State，簡單來說就是 vuex 的 state (狀態)要在 Compment 取得時候用 `computed` 去做 mapState 動作。

~~等等，程式是死的，人是活的，可以在 methods 呼叫?~~
這當然是可以，但為了程式可用性，最好不要用在別的地方。
好了，當然我用在 methods 上面不能使用。應該跟內部程式有關係。

`mapState({count:state=>state.count}).count` 也能讀到資料。但正常不會正樣用


## Getter

有點像 一般 Component 的 `computed`。

### 

main.js (官方這邊文件有寫錯...)
```
const store = createStore({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos(state){
      return state.todos.filter(todo => todo.done)
    }
  }
})

console.log( store.getters.doneTodos[0].id)
console.log('store.getters.doneTodos :' + store.getters.doneTodos )


```


GIT: [https://github.com/malagege/vuex-test/commit/750360bfa2a97e02aa1c84708a33edfe9718366e](https://github.com/malagege/vuex-test/commit/750360bfa2a97e02aa1c84708a33edfe9718366e)

### 通過屬性訪問

```javascript=
getters: {
  // ...
  doneTodosCount (state, getters) {
    return getters.doneTodos.length
  }
}
```

這邊比較特別看第二參數是用`getters`，裡面可以用 getters 變數

當然也可以用

```javascript=
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```


### 通過方法訪問(特別)


```javascript=
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
```
GIT: [https://github.com/malagege/vuex-test/commit/536f50d2aab41bf5448b6f617f124a8913a808aa]()


簡單說，Function 裡面回傳 Function 。還滿特別用法。


### mapGetters 輔助函數

也是用在`computed`。


```javascript=
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用對象展開運算符將 getter 混入 computed 對象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}

```


```javascript=
...mapGetters({
  // 把 `this.doneCount` 映射為 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})

```



## Mutation

> 提交 mutations 是改變 Vuex 中 store 的唯一方式。 mutations 非常類似於組件中的事件（event），每個 mutation 都有一個字串的 事件類型 (type) 和一個回調函數 (handler)， handler 就是我們實際進行狀態更改的地方，並且他會接受 state 作為第一個參數

[[Vue.js] Vuex 學習筆記 (7) - mutations 的核心概念 - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10191451)[備份圖](https://user-images.githubusercontent.com/6058558/123091931-8053fe00-d45c-11eb-90b5-8c1679840b60.png)


這邊我原本看不懂這句`mutations 非常類似於組件中的事件（event），每個 mutation 都有一個字串的 事件類型 (type) 和一個回調函數 (handler)`，後來看到上面文字敘述才了解。

簡單來說，`事件類型 (type)`我們常用 JS Event 都是用 `trigger` 去觸發事件，Vuex 是用 `store.commit`，`一個回調函數 (handler)`是指 store 裡的 mutations 的 function ，type命名在function 上面。這樣看真的很像 Event。

可參照上面敘述看下面程式:
```javascript=
const store = createStore({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})

```

呼叫 mutation 要用

```javascript=
store.commit('increment')
```

![圖片](https://user-images.githubusercontent.com/6058558/123091878-6e725b00-d45c-11eb-9e00-9be9e9c1fd0b.png)

GIT: [https://github.com/malagege/vuex-test/commit/00bd0b93a7ffb3d41a413eabc519cb15a3a2df2f](https://github.com/malagege/vuex-test/commit/536f50d2aab41bf5448b6f617f124a8913a808aa)

### 什麼是 payload

> payload意思即為承載量，在開發中則是指出在一堆資料中我們所關心的部分!
> google到一篇很好的文章對payload為何這樣叫有很好的解釋，文中指出這個名詞是借用運輸工具上的觀念而來的，例如：卡車、油罐車、貨輪等所謂的載具，然後通常一個載具的總重量一定大於載具的承載量，例如油罐車的總重量包含了他所運載的油量、司機的重量、油罐車行駛所需的油量，但我們所關心僅是油罐車所承載的油量而已。

簡單來說，當作一個data的主體，例如像健保卡一樣，裡面存放資料。

[開發中，常見的參數payload是什麼? - Noel Saga](http://noelsaga.herokuapp.com/blog/2015/07/18/kai-fa-zhong-,chang-jian-de-can-shu-payloadshi-shi-mo)

### 提交載荷(payload)

> 你可以向 store.commit 傳入額外的參數，即 mutation 的載荷（payload）：

```javascript=
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}

```

```javascript=
store.commit('increment', 10)
```


GIT: [https://github.com/malagege/vuex-test/commit/e04820c2cd0245e0faee9364ecf0aa927c9019bc](https://github.com/malagege/vuex-test/commit/e04820c2cd0245e0faee9364ecf0aa927c9019bc)

> 在大多數情況下，載荷應該是一個**物件**，這樣可以包含多個字段並且記錄的 mutation 會更易讀：

```javascript=
// ...
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}

```

```javascript=
store.commit('increment', {
  amount: 10
})
```

GIT: [https://github.com/malagege/vuex-test/commit/d033910dafe268599615e67d374ecb33709cc5dc](https://github.com/malagege/vuex-test/commit/d033910dafe268599615e67d374ecb33709cc5dc)


## commit 使用物件傳值

```javascript=
store.commit({
  type: 'increment',
  amount: 10
})
```

```javascript=
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

GIT: [https://github.com/malagege/vuex-test/commit/caa624c19d4741924f3abe5c9f6d0f5b93cb34f3](https://github.com/malagege/vuex-test/commit/caa624c19d4741924f3abe5c9f6d0f5b93cb34f3)



### 使用常量替代 Mutation 事件類型

> 使用常量替代 mutation 事件類型在各種 Flux 實現中是很常見的模式。這樣可以使 linter 之類的工具發揮作用，同時把這些常量放在單獨的文件中可以讓你的代碼合作者對整個 app 包含的 mutation 一目瞭然：

```javascript=
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'

```

```javascript=
// store.js
import { createStore } from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = createStore({
  state: { ... },
  mutations: {
    // 我們可以使用 ES2015 風格的計算屬性命名功能來使用一個常量作為函數名
    [SOME_MUTATION] (state) {
      // 修改 state
    }
  }
})

```

> 用不用常量取決於你——在需要多人協作的大型項目中，這會很有幫助。但如果你不喜歡，你完全可以不這樣做。


GIT: [https://github.com/malagege/vuex-test/commit/00841e0ede5e9473df641eff3646f69dade93256](https://github.com/malagege/vuex-test/commit/00841e0ede5e9473df641eff3646f69dade93256)


### Mutation 必須是同步函數

> 一條重要的原則就是要記住 mutation 必須是同步函數。為什麼？請參考下面的例子：

```javascript=
mutations: {
  someMutation (state) {
    api.callAsyncMethod(() => {
      state.count++
    })
  }
}
```

> 現在想像，我們正在 debug 一個 app 並且觀察 devtool 中的 mutation 日誌。每一條 mutation 被記錄，devtools 都需要捕捉到前一狀態和後一狀態的快照。然而，在上面的例子中 mutation 中的異步函數中的回調讓這不可能完成：因為當 mutation 觸發的時候，回調函數還沒有被調用，devtools 不知道什麼時候回調函數實際上被調用——實質上任何在回調函數中進行的狀態的改變都是不可追蹤的。


這邊參考範例調整:[手把手教你使用Vuex，猴子都能看懂的教程](https://juejin.cn/post/6928468842377117709)




```javascript=
// 創建一個新的 store 實例
const store = createStore({
  state: {
      name: '張三',
      number: 0,
  },
  mutations: {
      setNumberIsWhat(state, payload) {
          setTimeout(() => {
            state.number = payload.number;
          }, 1000);
      },
  }
});

  
console.log(`舊值：${store.state.number}`);

store.commit('setNumberIsWhat',{
  number: 20
})

// await store.dispatch('setNum');
console.log(`新值：${store.state.number}`);

```

#### 非同步測試

```javascript=
  mutations: {
      setNumberIsWhat(state, payload) {
          setTimeout(() => {
            state.number = payload.number;
          }, 1000);
      },
  }
```


結果:
![](https://i.imgur.com/JbCEPWh.png)

GIT: [https://github.com/malagege/vuex-test/commit/739138bbef8a7ed162456fafbf7a9d5808b4f1d2](https://github.com/malagege/vuex-test/commit/739138bbef8a7ed162456fafbf7a9d5808b4f1d2)

#### 同步測試

```javascript=
  mutations: {
      setNumberIsWhat(state, payload) {
          // setTimeout(() => {
            state.number = payload.number;
          // }, 1000);
      },
  }
```

結果:
![](https://i.imgur.com/SMoKaQW.png)


這邊結果來看，官方`Mutition`為什麼會關連`DevTools`原因所在。

![](https://i.imgur.com/HBeN483.png)


###  mapMutation


```javascript=
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 將 `this.increment()` 映射為 `this.$store.commit('increment')`

      // `mapMutations` 也支持載荷：
      'incrementBy' // 將 `this.incrementBy(amount)` 映射為 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 將 `this.add()` 映射為 `this.$store.commit('increment')`
    })
  }
}

```


用法跟之前差不多。


### 心得整理

其實當初看的時候一直在想為什麼要分 `Mutation` 和 `Action`，其實不照這樣做，程式也是可以使用的，但是這樣做的話，程式可能不容易看，維護上也不方便。`Mutation`觸發是透過`store.commit`去操作，在使用上跟 Web 觸發事件(Event)很像。

奇怪?我的程式沒有很複雜動作，一定要經過 Action 嗎?目前我看，應該也可以`Component method`，畢竟官方都給了 `mapMutation`不用嗎?XD



## Action


>Action 類似於 mutation，不同在於：
>
>  -  Action 提交的是 mutation，而不是直接變更狀態(state)。
>  -  Action 可以包含任意異步操作。

```javascript=
const store = createStore({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})

```


> Action 函數接受一個與 store 實例具有相同方法和屬性的 context 對象，因此你可以調用 context.commit 提交一個 mutation，或者通過 context.state 和 context.getters 來獲取 state 和 getters。當我們在之後介紹到 Modules 時，你就知道 context 對象為什麼不是 store 實例本身了。


> 實踐中，我們會經常用到 ES2015 的參數解構來簡化代碼（特別是我們需要調用 commit 很多次的時候）：

[lukehoban/es6features: Overview of ECMAScript 6 features](https://github.com/lukehoban/es6features#destructuring)

```javascript=
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```


### 分發 Action

> Action 通過 store.dispatch 方法觸發

```javascript=
store.dispatch('increment')
```

![](https://i.imgur.com/dfPPjVP.png)

GIT: [https://github.com/malagege/vuex-test/commit/637dfc3482613d86027ee32af369ede218793438](https://github.com/malagege/vuex-test/commit/637dfc3482613d86027ee32af369ede218793438)

> 乍一眼看上去感覺多此一舉，我們直接分發 mutation 豈不更方便？實際上並非如此，還記得 mutation 必須同步執行這個限制麼？Action 就不受約束！我們可以在 action 內部執行異步操作：

```javascript=
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

> Actions 支持同樣的載荷(payload)方式和物件方式進行分發：

```javascript=
// 以載荷形式分發
store.dispatch('incrementAsync', {
  amount: 10
})

// 以物件形式分發
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})

```

GIT: [https://github.com/malagege/vuex-test/commit/c0de63b661c0addc1edc6e24da3cb920cd242ab3](https://github.com/malagege/vuex-test/commit/c0de63b661c0addc1edc6e24da3cb920cd242ab3)

> 來看一個更加實際的購物車示例，涉及到調用異步 API 和分發多重 mutation：

```javascript=
actions: {
  checkout ({ commit, state }, products) {
    // 把當前購物車的物品備份起來
    const savedCartItems = [...state.cart.added]
    // 發出結賬請求，然後樂觀地清空購物車
    commit(types.CHECKOUT_REQUEST)
    // 購物 API 接受一個成功回調和一個失敗回調
    shop.buyProducts(
      products,
      // 成功操作
      () => commit(types.CHECKOUT_SUCCESS),
      // 失敗操作
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}
```



### 在組件中分發 Action(mapActions)

> 你在組件中使用 `this.$store.dispatch('xxx')` 分發 action，或者使用 mapActions 輔助函數將組件的 methods 映射為 store.dispatch 調用（需要先在根節點注入 store）：

```javascript=
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 將 `this.increment()` 映射為 `this.$store.dispatch('increment')`

      // `mapActions` 也支持載荷：
      'incrementBy' // 將 `this.incrementBy(amount)` 映射為 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 將 `this.add()` 映射為 `this.$store.dispatch('increment')`
    })
  }
}

```

### 組合 Action(重點)

> Action 通常是異步的，那麼如何知道 action 什麼時候結束呢？更重要的是，我們如何才能組合多個 action，以處理更加複雜的異步流程？

>　首先，你需要明白 store.dispatch 可以處理被觸發的 action 的處理函數返回的 Promise，並且 store.dispatch 仍舊返回 Promise：

```javascript=
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
```

> 現在你可以：

```javascript=
store.dispatch('actionA').then(() => {
  // ...
})
```

> 在另外一個 action 中也可以：

```javascript=
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```

> 最後，如果我們利用 async / await，我們可以如下組合 action：

```javascript=
// 假設 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}

```

GIT: [https://github.com/malagege/vuex-test/commit/4d639191a71bd674fffc7593a097f44673dcdf63](https://github.com/malagege/vuex-test/commit/4d639191a71bd674fffc7593a097f44673dcdf63)

![](https://i.imgur.com/YWfOr7k.png)


### Mutation / Action 差異

| \                | Mutation | Action   |
|------------------|----------|----------|
| 呼叫               | commit   | dispatch |
| 呼叫Function 第一個參數 | State    | Context  |
| 限制               | 不可用非同步   | 可用非同步    |

乍看之下，Mutation 和 Action 真的很像，但是 Mutation 主要修改 State ，所以 Function 第一個參數是 `State`。Action 可以做很多事情(取API資料、取 State 資料...)，我原本以為取 State 都要在所有 Component 去做，但 Action 應該也能做到??但感覺用外面帶進去會比較好?(符合DI原則)，之後有確定答案再補充...

### mapMutations,mapActions 會自動映射 payload

實作使用mapMutations, mapActions，發現想帶入 payload ，但mapMutations / mapActions 都可以映射參數。官方文件就有說明。~~一不小心就漏掉，我真是後知後覺~~

```javascript=
    ...mapActions([
      // `mapActions` 也支持載荷：
      'incrementBy' // 將 `this.incrementBy(amount)` 映射為 `this.$store.dispatch('incrementBy', amount)`
    })
```


### mapState,mapGetters,mapMutations,mapActions

這邊我發現 mapXXX，只有 State 是單數，其他都是複數。我覺得我之後可能會忘記，特別說一下。簡單來說，State 只有一個，Getters,Mutations,Actions有很多 function 組成。這樣記應該會比較好。


### 該加強 Promise / Async

看本篇 Action 範例，有許多 Promse / Async 用法，讓我覺得還可以這樣用!!可能有空要加強一下，希望自己使用不會用到爆炸，哈哈。


## Module

看到很多教學沒有交到 Module，因為我覺得 Vuex 文章不多，想說把他看完。除非遇到不會的，例如 v-solt 不會，就先跳過。

> 為瞭解決以上問題，Vuex 允許我們將 store 分割成模塊（module）。每個模塊擁有自己的 state、mutation、action、getter、甚至是嵌套子模塊——從上至下進行同樣方式的分割：

```javascript=
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = createStore({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的狀態
store.state.b // -> moduleB 的狀態

```

### 模塊的局部狀態(使用rootState)

> 對於模塊內部的 mutation 和 getter，接收的第一個參數是模塊的局部狀態對象。

#### 一般常用第一參數

```javascript=
const moduleA = {
  state: () => ({
    count: 0
  }),
  mutations: {
    increment (state) {
      // 這裡的 `state` 對象是模塊的局部狀態
      state.count++
    }
  },

  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}

```


#### Action

```javascript=
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}

```



#### Getter

```javascript=
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}

```


### 命名空間(預設全域)

Getter , Action, Mutation 都是全局宣告的。仔細想想也對，像 JavaScript 也是全域宣告 Event。

Vuex 也有做 `namesapce`， 參考官方範例也不難。 

觀察幾點記錄一下:

  1. 這邊我自己看 `namespace: true`，只宣告 `moduole`某屬性上面，不會加在 跟 store 上面，因為在跟 store 沒有甚麼意義。
  2. module 裡面載入別的 module 會繼承父模塊的命名空間。參考下面官方範例: myPage
  3. 內層 module 可以重新命名空間。參考下面範例: posts
    
```javascript=
const store = createStore({
  modules: {
    account: {
      namespaced: true,

      // 模塊內容（module assets）
      state: () => ({ ... }), // 模塊內的狀態已經是嵌套的了，使用 `namespaced` 屬性不會對其產生影響
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模塊
      modules: {
        // 繼承父模塊的命名空間
        myPage: {
          state: () => ({ ... }),
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 進一步嵌套命名空間
        posts: {
          namespaced: true,

          state: () => ({ ... }),
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})

```


> 啟用了命名空間的 getter 和 action 會收到局部化的 getter，dispatch 和 commit。換言之，你在使用模塊內容（module assets）時不需要在同一模塊內額外添加空間名前綴。更改 namespaced 屬性後不需要修改模塊內的代碼。

下面範例會看到，再進行比較。

#### 在帶命名空間的模塊內訪問全域內容（Global Assets）

> 如果你希望使用全局 state 和 getter，**rootState 和 rootGetters 會作為第三和第四參數傳入 getter，也會通過 context 對象的屬性傳入 action。**

> 若需要在全局命名空間內分發 action 或提交 mutation，將 { root: true } 作為第三參數傳給 dispatch 或 commit 即可。

參考官方範例就會了解。

```javascript=
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在這個模塊的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四個參數來調用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在這個模塊中， dispatch 和 commit 也被局部化了
      // 他們可以接受 `root` 屬性以訪問根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```

這邊還有一點比較特別，**若需要在全局命名空間內分發 action 或提交 mutation，將 { root: true } 作為第三參數傳給 dispatch 或 commit 即可。**

```javascript=
        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
```


###  在帶命名空間的模塊註冊全局 action

> 若需要在帶命名空間的模塊註冊全局 action，你可添加 root: true，並將這個 action 的定義放在函數 handler 中。例如：

```javascript=
{
  actions: {
    someOtherAction ({dispatch}) {
      dispatch('someAction')
    }
  },
  modules: {
    foo: {
      namespaced: true,

      actions: {
        someAction: {
          root: true,
          handler (namespacedContext, payload) { ... } // -> 'someAction'
        }
      }
    }
  }
}

```

老實說不太了解這邊能印用在哪邊，但是可以透過子組件宣告全域Action 事件。


### 帶命名空間的綁定函數

> 當使用 mapState、mapGetters、mapActions 和 mapMutations 這些函數來綁定帶命名空間的模塊時，寫起來可能比較繁瑣：

```javascript=
computed: {
  ...mapState({
    a: state => state.some.nested.module.a,
    b: state => state.some.nested.module.b
  })
},
methods: {
  ...mapActions([
    'some/nested/module/foo', // -> this['some/nested/module/foo']()
    'some/nested/module/bar' // -> this['some/nested/module/bar']()
  ])
}
```

> }

對於這種情況，你可以將模塊的空間名稱字符串作為第一個參數傳遞給上述函數，這樣所有綁定都會自動將該模塊作為上下文。於是上面的例子可以簡化為：

```javascript=
computed: {
  ...mapState('some/nested/module', {
    a: state => state.a,
    b: state => state.b
  })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo', // -> this.foo()
    'bar' // -> this.bar()
  ])
}
```

> 而且，你可以通過使用 createNamespacedHelpers 創建基於某個命名空間輔助函數。它返回一個對象，對象裡有新的綁定在給定命名空間值上的組件綁定輔助函數：

```javascript=
import { createNamespacedHelpers } from 'vuex'

const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}

```

後面就先跳過。[Module | Vuex](https://next.vuex.vuejs.org/zh/guide/modules.html#%E7%BB%99%E6%8F%92%E4%BB%B6%E5%BC%80%E5%8F%91%E8%80%85%E7%9A%84%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)

## 項目結構

做前面其實我大概知道 store 可以拆出來一個 js，但我為了方便測試還是先放 main.js。

官方還很好貼個範例: [vuex/examples/classic/shopping-cart at 4.0 · vuejs/vuex](https://github.com/vuejs/vuex/tree/4.0/examples/classic/shopping-cart)

```javascript=
├── index.html
├── main.js
├── api
│   └── ... # 抽取出API請求
├── components
│   ├── App.vue
│   └── ...
└── store
    ├── index.js          # 我們組裝模塊並導出 store 的地方
    ├── actions.js        # 根級別的 action
    ├── mutations.js      # 根級別的 mutation
    └── modules
        ├── cart.js       # 購物車模塊
        └── products.js   # 產品模塊
```


##  組合式API

```javascript=
import { useStore } from 'vuex'

export default {
  setup () {
    const store = useStore()
  }
}


```

這邊用法就跟之前差不多，所以就不貼了。可參考:[組合式API | Vuex](https://next.vuex.vuejs.org/zh/guide/composition-api.html#%E8%AE%BF%E9%97%AE-state-%E5%92%8C-getter)

官方範例:[vuex/examples/composition at 4.0 · vuejs/vuex](https://github.com/vuejs/vuex/tree/4.0/examples/composition)


## 嚴格模式

```javascript=
const store = createStore({
  // ...
  strict: true
})

```

![](https://i.imgur.com/PmhNNkR.png)


### 開發環境與發佈環境

```javascript=
const store = createStore({
  // ...
  strict: process.env.NODE_ENV !== 'production'
})

```


## 表單處理

```htmlembedded=
<input v-model="message">
```

```javascript=
computed: {
  message: {
    get () {
      return this.$store.state.obj.message
    },
    set (value) {
      this.$store.commit('updateMessage', value)
    }
  }
}

```

[表单处理 | Vuex](https://next.vuex.vuejs.org/zh/guide/forms.html)