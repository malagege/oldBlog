---
title: ' Vue Router 4 學習筆記'
date: 2021-06-27 14:44:45
tags: [vue,vue router]
categories: Vue
---

這篇 Vue Router 4 學習筆記，大部份都是抄[官方](https://next.router.vuejs.org/zh/)文件內容，有簡單實作小記，可參考 GIT 練習，算是自己學習重點筆記。

<!--more-->

## 安裝 Vue router 4

```bash=
npm install vue-router@4
```

設定 main.js
```javascript=
import { createApp } from 'vue'
import { createRouter, createWebHashHistory } from 'vue-router'
import App from './App.vue'
import Home from './components/Home.vue'
import About from './components/About.vue'

// 2. 定義一些路由
// 每個路由都需要映射到一個組件。
// 我們後面再討論嵌套路由。
const routes = [
    { path: '/', component: Home },
    { path: '/about', component: About },
  ]
  
// 3. 創建路由實例並傳遞 `routes` 配置
// 你可以在這裡輸入更多的配置，但我們在這裡
// 暫時保持簡單
const router = createRouter({
    // 4. 內部提供了 history 模式的實現。為了簡單起見，我們在這裡使用 hash 模式。
    history: createWebHashHistory(),
    routes, // `routes: routes` 的縮寫
  })
  

createApp(App).use(router).mount('#app')

```

重點提醒:
1. import { createRouter, createWebHashHistory } from 'vue-router'
2.  routes 宣告
3. router 宣告(使用 createRouter, createWebHashHistory)
4. createApp use vue-router


App.vue
```htmlembedded=
<template>
  <h1>Hello App!</h1>
  <p>
    <!--使用 router-link 組件進行導航 -->
    <!--通過傳遞 `to` 來指定鏈接 -->
    <!--`<router-link>` 將呈現一個帶有正確 `href` 屬性的 `<a>` 標籤-->
    <router-link to="/">Go to Home</router-link>
    <router-link to="/about">Go to About</router-link>
  </p>
  <!-- 路由出口 -->
  <!-- 路由匹配到的組件將渲染在這裡 -->
  <router-view></router-view>
</template>

<script setup>
import Home from './components/Home.vue'
import About from './components/About.vue'
import HelloWorld from './components/HelloWorld.vue'

// This starter template is using Vue 3 experimental <script setup> SFCs
// Check out https://github.com/vuejs/rfcs/blob/script-setup-2/active-rfcs/0000-script-setup.md
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```

重點提醒
1. `<router-link to="/">Go to Home</router-link>` 超連結指定某路由
2. `<router-view></router-view>` 路由顯示的Vue元件


GIT:[8d7a44b6059b3caf6ad648056817af4c8ec9060c](https://github.com/malagege/vue-router-test/commit/8d7a44b6059b3caf6ad648056817af4c8ec9060c)

> 要在 setup 函數中訪問路由，請調用 useRouter 或 useRoute 函數。我們將在 Composition API 中瞭解更多信息。
> 在整個文檔中，我們會經常使用 router 實例，請記住，this.$router 與直接使用通過 createRouter 創建的 router 實例完全相同。我們使用 this.\$router 的原因是，我們不想在每個需要操作路由的組件中都導入路由。

引用一下官方這段，setup 函數可以用 `useRouter` 和 `useRoute`。 router 是指整個 router實例。 route 是指 `routes`某一個 route 設定。


## 動態路由匹配

[带参数的动态路由匹配 | Vue Router](https://next.router.vuejs.org/zh/guide/essentials/dynamic-matching.html)

### 帶參數動態路由

使用 $route.param

| 匹配模式                           | 匹配路徑                     | $route.params                          |
|--------------------------------|--------------------------|----------------------------------------|
| /users/:username               | /users/eduardo           | { username: 'eduardo' }                |
| /users/:username/posts/:postId | /users/eduardo/posts/123 | { username: 'eduardo', postId: '123' } |


GIT:https://github.com/malagege/vue-router-test/commit/b1afcbb8fe9240e4851a680db958fafc558b6dd9

### 響應路由參數的變化(重要)

取得資料就是 AJAX 可用這個方式取得，後面會講到。

```javascript=
export default {
  created() {
    this.$watch(
      () => this.$route.params,
      (toParams, previousParams) => {
        console.log('toParams',toParams,'previsousParams',previousParams);
      }
    )
  },
}
```

watch 跟 $watch 差別

>    - $watch : 實體上的函數，使用此函數註冊監聽器。
>    - watch : 實體上的屬性，此屬性設置的物件在實體建立時會叫用 $watch 註冊監聽器。
>
> $watch 是註冊監聽器的函數，而 watch 是為了開發者方便在實體上設置監聽器而提供的，其實 watch 本身也是使用 $watch 註冊監聽器。

參考:
- [監聽器( watch ) | 勇者鬥 Vue 龍](https://peterhpchen.github.io/VuejsQuest/basic/08_Watcher.html#%E4%BB%8B%E7%B4%B9)
- [vue中watch和vm.$watch方法的區別？ - SegmentFault 思否](https://segmentfault.com/q/1010000023828086)


使用 beforeRouteUpdate 導航守衛
```javascript=
//   使用 beforeRouteUpdate 導航守衛
  async beforeRouteUpdate(to, from) {
    // 對路由變化做出響應...
    // this.userData = await fetchUser(to.params.id)
    console.log('to',to,'from',from);
  },
}
```

簡單說明，第一次進來不會觸發上面兩個 function ，但第二次就會了。
![](https://i.imgur.com/FK9JlKb.png)


GIT: [9649555832b6732ed355f765b0047e733294f3a4](https://github.com/malagege/vue-router-test/commit/9649555832b6732ed355f765b0047e733294f3a4)

###  捕獲所有路由或 404 Not found 路由



```javascript=
const routes = [
  // 將匹配所有內容並將其放在 `$route.params.pathMatch` 下
  { path: '/:pathMatch(.*)*', name: 'NotFound', component: NotFound },
  // 將匹配以 `/user-` 開頭的所有內容，並將其放在 `$route.params.afterUser` 下
  { path: '/user-:afterUser(.*)', component: UserGeneric },
]

```

```javascript=
this.$router.push({
  name: 'NotFound',
  params: { pathMatch: this.$route.path.split('/') },
})

```

這邊我改變一下

```javascript=
export default {
    methods:{
        test(){
            console.log(this.$route.path.split('/'));
            this.$router.push({
            name: 'NotFound',
            // :pathMatch 為 下面參數可以帶入
            // http://localhost:3000/#/123/456
            params: { pathMatch: ['123','456'] },
            })
        }
    }
}
```


GIT:[ef8040a99c655c68c20b92e8aceb47f64b7403c7](https://github.com/malagege/vue-router-test/commit/ef8040a99c655c68c20b92e8aceb47f64b7403c7)


## 路由的匹配語法

[路由的匹配語法 | Vue Router](https://next.router.vuejs.org/zh/guide/essentials/route-matching-syntax.html)

基本上就是用正規表達式設定路由。這邊就不簡單實作

```javascript=
const routes = [
  // /:orderId -> 僅匹配數字
  { path: '/:orderId(\\d+)' },
  // /:productName -> 匹配其他任何內容
  { path: '/:productName' },
]

```


###  可重複的參數

```javascript=
const routes = [
  // /:chapters ->  匹配 /one, /one/two, /one/two/three, 等
  { path: '/:chapters+' },
  // /:chapters -> 匹配 /, /one, /one/two, /one/two/three, 等
  { path: '/:chapters*' },
]

```

router.resolve 組出 url

```javascript=
// 給定 { path: '/:chapters*', name: 'chapters' },
router.resolve({ name: 'chapters', params: { chapters: [] } }).href
// 產生 /
router.resolve({ name: 'chapters', params: { chapters: ['a', 'b'] } }).href
// 產生 /a/b

// 給定 { path: '/:chapters+', name: 'chapters' },
router.resolve({ name: 'chapters', params: { chapters: [] } }).href
// 拋出錯誤，因為 `chapters` 為空 

```

`+`與`*` 的差別

```javascript=
const routes = [
  // 僅匹配數字
  // 匹配 /1, /1/2, 等
  { path: '/:chapters(\\d+)+' },
  // 匹配 /, /1, /1/2, 等
  { path: '/:chapters(\\d+)*' },
]

```

### 可選參數

> 請注意，* 在技術上也標誌著一個參數是可選的，但 ? 參數不能重複。

```javascript=
const routes = [
  // 匹配 /users 和 /users/posva
  { path: '/users/:userId?' },
  // 匹配 /users 和 /users/42
  { path: '/users/:userId(\\d+)?' },
]

```


## 嵌套路由(nested-routes)

![](https://i.imgur.com/Djd7ZY4.png)

兩個步驟

1. 設定 router 的 children 屬性

```javascript=
const routes = [
    { path: '/', component: Home },
    { path: '/about', component: About },
    // 動態段以冒號開始
    { path: '/users/:id', component: User ,
      children: [
        {
          // 當 /user/:id/profile 匹配成功 
          // UserProfile 將被渲染到 User 的 <router-view> 內部
          path: 'profile',
          component: UserProfile,
        },
        {
          // 當 /user/:id/posts 匹配成功
          // UserPosts 將被渲染到 User 的 <router-view> 內部
          path: 'posts',
          component: UserPosts,
        },
      ],
    },
```

2. 設定目標組件(父層) router-view

```htmlembedded=
    <div>
        User  {{ $route.params.id }}
        <br>
        <button v-on:click="test"> on test</button>
        <router-view></router-view>
    </div>
```

連結: http://localhost:3000/#/users/1/profile


GIT:[a0ee324aefb696a91645c1258181979cd7c35277](https://github.com/malagege/vue-router-test/commit/)

### 設定嵌套路由首頁

```javascript=
const routes = [
    { path: '/', component: Home },
    { path: '/about', component: About },
    // 動態段以冒號開始
    { path: '/users/:id', component: User ,
      children: [
        // UserHome will be rendered inside User's <router-view>
        // when /user/:id is matched
        // 當 /user/:id 匹配成功
        // UserHome 將被渲染到 User 的 <router-view> 內部
        { path: '', component: UserHome },
```

連結: http://localhost:3000/#/users/1/

GIT:[1d362c35c2ad53966a8d1794e845f7a07fc5eafa](https://github.com/malagege/vue-router-test/commit/1d362c35c2ad53966a8d1794e845f7a07fc5eafa)

官方範例:[Nested Views - Vue Router 4 examples - CodeSandbox](https://codesandbox.io/s/nested-views-vue-router-4-examples-hl326?initialpath=/users/eduardo&file=/src/main.js)


## 編程式導航(Navigation Guards)

### 跳頁到不同路徑網址

原本標題「導航到不同的位置」，我覺得看不是很懂，看我改得比較白話，哈哈。

> 注意：在 Vue 實例中，你可以通過 \$router 訪問路由實例。因此你可以調用 this.$router.push。

> 想要導航到不同的 URL，可以使用 router.push 方法。這個方法會向 history 棧添加一個新的記錄，所以，當用戶點擊瀏覽器後退按鈕時，會回到之前的 URL。

> 當你點擊 `<router-link>` 時，內部會調用這個方法，所以點擊 `<router-link :to="...">` 相當於調用 router.push(...) ：

| 聲明式                     | 編程式              |
|-------------------------|------------------|
| <router-link :to="..."> | router.push(...) |




```javascript=
// 字符串路徑
router.push('/users/eduardo')

// 帶有路徑的對象
router.push({ path: '/users/eduardo' })

// 命名的路由，並加上參數，讓路由建立 url
router.push({ name: 'user', params: { username: 'eduardo' } })

// 帶查詢參數，結果是 /register?plan=private
router.push({ path: '/register', query: { plan: 'private' } })

// 帶 hash，結果是 /about#team
router.push({ path: '/about', hash: '#team' }
```



比較特別的是後面三個，用`query`和`hash`屬性前面沒帶過

稍微查了一下，[這篇提到](https://blog.csdn.net/weixin_45846862/article/details/112182113)裡面提到使用`this.$route.query.參數`也可以抓到值

[API Reference | Vue Router](https://next.router.vuejs.org/api/#query)


```javascript
const username = 'eduardo'
// 我們可以手動建立 url，但我們必須自己處理編碼
router.push(`/user/${username}`) // -> /user/eduardo
// 同樣
router.push({ path: `/user/${username}` }) // -> /user/eduardo
// 如果可能的話，使用 `name` 和 `params` 從自動 URL 編碼中獲益
router.push({ name: 'user', params: { username } }) // -> /user/eduardo
// `params` 不能與 `path` 一起使用
router.push({ path: '/user', params: { username } }) // -> /user

```

我有嘗試使用參數帶中文，但沒有發現什麼問題

GIT:[75bf26ebf64ec5c2c4dc6f5476e86bb517327e65](https://github.com/malagege/vue-router-test/commit/75bf26ebf64ec5c2c4dc6f5476e86bb517327e65)

> 由於屬性 to 與 router.push 接受的對象種類相同，所以兩者的規則完全相同。
> router.push 和所有其他導航方法都會返回一個 Promise，讓我們可以等到導航完成後才知道是成功還是失敗。我們將在 Navigation Handling 中詳細介紹。

### 替換當前位置

> 它的作用類似於 router.push，唯一不同的是，它在導航時不會向 history 添加新記錄，正如它的名字所暗示的那樣——它取代了當前的條目。

| 聲明式                             | 編程式                 |
|---------------------------------|---------------------|
| <router-link :to="..." replace> | router.replace(...) |


```javascript=
router.push({ path: '/home', replace: true })
// 相當於
router.replace({ path: '/home' })
```

### 回前一頁、下一頁

> 該方法採用一個整數作為參數，表示在歷史堆棧中前進或後退多少步，類似於 `window.history.go(n)`

```javascript=
// 向前移動一條記錄，與 router.forward() 相同
router.go(1)

// 返回一條記錄，與router.back() 相同
router.go(-1)

// 前進 3 條記錄
router.go(3)

// 如果沒有那麼多記錄，靜默失敗
router.go(-100)
router.go(100)

```

## 命名路由

> 除了 path 之外，你還可以為任何路由提供 name。這有以下優點：
> 
>    - 沒有硬編碼的 URL
>    - params 的自動編碼/解碼。
>    - 防止你在 url 中出現打字錯誤。
>    - 繞過路徑排序（如顯示一個）

```javascript=
const routes = [
  {
    path: '/user/:username',
    name: 'user',
    component: User
  }
]

```

```htmlembedded=
<router-link :to="{ name: 'user', params: { username: 'erina' }}">
  User
</router-link>

```

與下面程式相同

```javascript=
router.push({ name: 'user', params: { username: 'erina' } }
```


[vue-router/app.js at dev · vuejs/vue-router](https://github.com/vuejs/vue-router/blob/dev/examples/named-routes/app.js)


## 命名視圖

簡單來說，設定 `router-view` 沒設定 name 後，預設名稱會是`default`。這邊看範例比較快。

```htmlembedded=
<router-view class="view left-sidebar" name="LeftSidebar"></router-view>
<router-view class="view main-content"></router-view>
<router-view class="view right-sidebar" name="RightSidebar"></router-view>

```

```javascript=
const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    {
      path: '/',
      components: {
        default: Home,
        // LeftSidebar: LeftSidebar 的縮寫
        LeftSidebar,
        // 它們與 `<router-view>` 上的 `name` 屬性匹配
        RightSidebar,
      },
    },
  ],
})

```

官方範例:[Named Views - Vue Router 4 examples - CodeSandbox](https://codesandbox.io/s/named-views-vue-router-4-examples-rd20l?file=/src/main.js)


### 嵌套命名視圖

我覺得這部分看官方文件比較清楚。

[命名視圖 | Vue Router](https://next.router.vuejs.org/zh/guide/essentials/named-views.html#%E5%B5%8C%E5%A5%97%E5%91%BD%E5%90%8D%E8%A7%86%E5%9B%BE)


官方範例: [Nested Named Views - Vue Router 4 examples - CodeSandbox](https://codesandbox.io/s/nested-named-views-vue-router-4-examples-re9yl?&initialpath=%2Fsettings%2Femails)

## 重新導向和别名

官方文件原翻譯: 重定向和别名


### 重新導向(redirect)

/search/screens -> /search?q=screens
 
跟後端 redirect 意思差不多

```
const routes = [{ path: '/home', redirect: '/' }]
```

也能指向一個 route 的名稱

```
const routes = [{ path: '/home', redirect: { name: 'homepage' } }]

```

#### 動態導向(帶參數)

```javascript=
const routes = [
  {
    // /search/screens -> /search?q=screens
    path: '/search/:searchText',
    redirect: to => {
      // 方法接收目標路由作為參數
      // return 重定向的字符串路徑/路徑對象
      return { path: '/search', query: { q: to.params.searchText } }
    },
  },
  {
    path: '/search',
    // ...
  },
]

```

#### 别名

`/` 參照 `/home`。

```
const routes = [{ path: '/', component: Homepage, alias: '/home' }]

```


> 通過別名，你可以自由地將 UI 結構映射到一個任意的 URL，而不受配置的嵌套結構的限制。**使別名以 / 開頭，以使嵌套路徑中的路徑成為絕對路徑。**你甚至可以將兩者結合起來，用一個數組提供多個別名：
```javascript=
const routes = [
  {
    path: '/users',
    component: UsersLayout,
    children: [
      // 為這 3 個 URL 呈現 UserList
      // - /users
      // - /users/list
      // - /people
      { path: '', component: UserList, alias: ['/people', 'list'] },
    ],
  },
]

```

SEO 需要注意的事情:[Consolidate Duplicate URLs with Canonicals | Google Search Central](https://developers.google.com/search/docs/advanced/crawling/consolidate-duplicate-urls?hl=en&visit_id=637596083090705419-2476927526&rd=1)


##  將 props 傳遞給路由組件(容易忘記?)

```javascript=
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const routes = [{ path: '/user/:id', component: User }]

```

替換

```javascript=
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const routes = [{ path: '/user/:id', component: User, props: true }]

```

簡單來說 route 設定多了 `props:true`，prop也需要設定對應變數


### Boolean mode

> 當 props 設置為 true 時，route.params 將被設置為組件的 props。

#### 命名視圖

簡單來說，跟上面用法差不多。

```javascript=
const routes = [
  {
    path: '/user/:id',
    components: { default: User, sidebar: Sidebar },
    props: { default: true, sidebar: false }
  }
]
```

### 物件給值(固定給值)

這邊是固定給值

```javascript=
const routes = [
  {
    path: '/promotion/from-newsletter',
    component: Promotion,
    props: { newsletterPopup: false }
  }
]

```


### 函式給值(重要?)

```javascript=
const routes = [
  {
    path: '/search',
    component: SearchUser,
    props: route => ({ query: route.query.q })
  }
]

````

官方範例:[vue-router/app.js at dev · vuejs/vue-router](https://github.com/vuejs/vue-router/blob/dev/examples/route-props/app.js)

這邊就不實戰，簡單做個結論，動態給值，可使用props,function 方式進去，固定方式可以用 object mode


## 歷史模式

都是Web 常見的兩種`hash`,`html5 history api`

### hash

```javascript=
import { createRouter, createWebHashHistory } from 'vue-router'

const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    //...
  ],
})

```

###  HTML5 模式

> 用 createWebHistory() 创建 HTML5 模式，推荐使用这个模式：


```javascript=
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    //...
  ],
})


```


### 伺服器配置

[不同的歷史模式 | Vue Router](https://next.router.vuejs.org/zh/guide/essentials/history-mode.html#%E6%9C%8D%E5%8A%A1%E5%99%A8%E9%85%8D%E7%BD%AE%E7%A4%BA%E4%BE%8B)

~~官方竟然沒放 Traefik ，我自己爬文找一下。~~
我竟然犯蠢了...，Traefik 不是 Web Server，所以我應該是要改代理後面的Web Server。

參考:[Nginx try_files to traefik ingressrout? - Traefik / Traefik v2 - Traefik Labs Community Forum](https://community.traefik.io/t/nginx-try-files-to-traefik-ingressrout/6915)

> Sorry, not clear.
> 
> Nginx is a web server not just a proxy like Traefik, and Traefik does not have a feature similar to try_files simply because it's not a web server.
>  
> Also "not working" is not a very good problem description. How do you test, what results are you expecting and what are you observing instead?


## 導航守衛(Navigation Guards)


### 全局前置守衛

```javascript=
const router = createRouter({ ... })

router.beforeEach((to, from) => {
  // ...
  // 返回 false 以取消導航
  return false
})

```

> 當一個導航觸發時，全局前置守衛按照創建順序調用。守衛是異步解析執行，此時導航在所有守衛 resolve 完之前一直處於等待中。

這邊有去做個實作，第一次進去，to 和 from 都會有值，這邊設定一半機率會跳頁!!簡單來說，to 是要轉頁面，from 是來源頁，當回傳 false時候，會轉到 from 。所以這就是為什麼第一次進去，from ,to 都是一樣，不會卡住問題

```javascript=
router.beforeEach((to, from) => {
  console.log('beforeEach START')
  console.log('to',to)
  console.log('from',from)
  console.log('beforeEach END')
  if(Math.random()> 0.5) return true
  // ...
  // 返回 false 以取消導航
  return false
})

```

> 每個守衛方法接收兩個參數：
> 
>    - to: 即將要進入的目標 用一種標準化的方式
>    - from: 當前導航正要離開的路由 用一種標準化的方式
> 
> 可以返回的值如下:
> 
>    - false: 取消當前的導航。如果瀏覽器的 URL 改變了(可能是用戶手動或者瀏覽器後退按鈕)，那麼 URL 地址會重置到 from 路由對應的地址。
>    - 一個路由地址: 通過一個路由地址跳轉到一個不同的地址，就像你調用 router.push() 一樣，你可以設置諸如 replace: true 或 name: 'home' 之類的配置。當前的導航被中斷，然後進行一個新的導航，就和 from 一樣。


### 錯誤監聽

> 如果遇到了意料之外的情況，可能會拋出一個 Error。這會取消導航並且調用 router.onError() 註冊過的回調。
> 
> 如果什麼都沒有，undefined 或返回 true，則導航是有效的，並調用下一個導航守衛

```javascript=
router.beforeEach((to, from) => {
  console.log('beforeEach START')
  console.log('to',to)
  console.log('from',from)
  console.log('beforeEach END')
  a[2]
  if(Math.random()> 0.5) return true
  // ...
  // 返回 false 以取消導航
  return false
})

router.onError((error,to,from)=>{
  console.log('Error START');
  console.log('error',error);
  console.log('to', to);
  console.log('from', from);
  console.log('Error END')
})

``` 


![](https://i.imgur.com/AI8VPxo.png)


#### 可用 Promise工作方式

```javascript=
router.beforeEach(async (to, from) => {
  // canUserAccess() 返回 `true` 或 `false`
  return await canUserAccess(to)
})
```


####  可選的第三個參數 next

> 在之前的 Vue Router 版本中，也是可以使用 第三個參數 next 的。這是一個常見的錯誤來源，可以通過 RFC 來消除錯誤。然而，它仍然是被支持的，這意味著你可以向任何導航守衛傳遞第三個參數。在這種情況下，確保 next 在任何給定的導航守衛中都被嚴格調用一次。它可以出現多於一次，但是只能在所有的邏輯路徑都不重疊的情況下，否則鉤子永遠都不會被解析或報錯。這裡有一個在用戶未能驗證身份時重定向到/login的錯誤用例：

```javascript=
// GOOD
router.beforeEach((to, from, next) => {
  if (to.name !== 'Login' && !isAuthenticated) next({ name: 'Login' })
  else next()
})

```

簡單來說，這邊簡單實作是否登入動作導向登入頁。`next()`裡面空值，會進去`to`頁面，假如有指定next裡面參數，會進去到指定頁面。

這邊我們看到，這邊沒有return ture/false，我很好奇我這邊return false會怎麼樣?結論，`next()`會執行跳頁動作，所以return true/false就沒有什麼意思。

```javascript=
router.beforeEach((to, from, next) => {
  if (to.name === 'about'){
    next()
  }else{
    next({ name: 'about' })
  }
  // else next()
  return false
})
```


### 全局解析守衛

> 你可以用 router.beforeResolve 註冊一個全局守衛。這和 router.beforeEach 類似，因為它在 每次導航時都會觸發，但是確保在導航被確認之前，同時在所有組件內守衛和異步路由組件被解析之後，解析守衛就被正確調用。這裡有一個例子，確保用戶可以訪問自定義 meta 屬性 requiresCamera 的路由：

```javascript=
router.beforeResolve(async to => {
  if (to.meta.requiresCamera) {
    try {
      await askForCameraPermission()
    } catch (error) {
      if (error instanceof NotAllowedError) {
        // ... 處理錯誤，然後取消導航
        return false
      } else {
        // 意料之外的錯誤，取消導航並把錯誤傳給全局處理器
        throw error
      }
    }
  }
})
```

這邊可以進行權限判斷。


### 全局後置鉤子

```javascript=
router.afterEach((to, from) => {
  sendToAnalytics(to.fullPath)
})
```

或

```javascript=
router.afterEach((to, from, failure) => {
  if (!failure) sendToAnalytics(to.fullPath)
})
```

[等待导航结果 | Vue Router](https://next.router.vuejs.org/zh/guide/advanced/navigation-failures.html)


#### 路由(route)獨享的守衛

```javascript=
const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: (to, from) => {
      // reject the navigation
      return false
    },
  },
]

```


> beforeEnter 守衛 只在進入路由時觸發，不會在 params、query 或 hash 改變時觸發。例如，從 /users/2 進入到 /users/3 或者從 /users/2#info 進入到 /users/2#projects。它們只有在 從一個不同的 路由導航時，才會被觸發。
> 
> 你也可以將一個函數數組傳遞給 beforeEnter，這在為不同的路由重用守衛時很有用：

```javascript=
function removeQueryParams(to) {
  if (Object.keys(to.query).length)
    return { path: to.path, query: {}, hash: to.hash }
}

function removeHash(to) {
  if (to.hash) return { path: to.path, query: to.query, hash: '' }
}

const routes = [
  {
    path: '/users/:id',
    component: UserDetails,
    beforeEnter: [removeQueryParams, removeHash],
  },
  {
    path: '/about',
    component: UserDetails,
    beforeEnter: [removeQueryParams],
  },
]

```

beforeEnter 這邊是設定在 route，可以帶路陣列方式。

#### 組件內的守衛

>  可用的配置 API
> 
> 你可以為路由組件添加以下配置：
> 
>    - beforeRouteEnter
>    - beforeRouteUpdate
>    - beforeRouteLeave


```javascript=
const UserDetails = {
  template: `...`,
  beforeRouteEnter(to, from) {
    // 在渲染該組件的對應路由被驗證前調用
    // 不能獲取組件實例 `this` ！
    // 因為當守衛執行時，組件實例還沒被創建！
  },
  beforeRouteUpdate(to, from) {
    // 在當前路由改變，但是該組件被覆用時調用
    // 舉例來說，對於一個帶有動態參數的路徑 `/users/:id`，在 `/users/1` 和 `/users/2` 之間跳轉的時候，
    // 由於會渲染同樣的 `UserDetails` 組件，因此組件實例會被覆用。而這個鉤子就會在這個情況下被調用。
    // 因為在這種情況發生的時候，組件已經掛載好了，導航守衛可以訪問組件實例 `this`
  },
  beforeRouteLeave(to, from) {
    // 在導航離開渲染該組件的對應路由時調用
    // 與 `beforeRouteUpdate` 一樣，它可以訪問組件實例 `this`
  },
}

```


常用 beforeRouteLeave


```javascript=
beforeRouteLeave (to, from) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (!answer) return false
}

```

### 完整的導航解析流程

    導航被觸發。
    在失活的組件裡調用 beforeRouteLeave 守衛。
    調用全局的 beforeEach 守衛。
    在重用的組件裡調用 beforeRouteUpdate 守衛(2.2+)。
    在路由配置裡調用 beforeEnter。
    解析異步路由組件。
    在被激活的組件裡調用 beforeRouteEnter。
    調用全局的 beforeResolve 守衛(2.5+)。
    導航被確認。
    調用全局的 afterEach 鉤子。
    觸發 DOM 更新。
    調用 beforeRouteEnter 守衛中傳給 next 的回調函數，創建好的組件實例會作為回調函數的參數傳入。
    
    


## 路由元信息

可針對 route 設定 meta 做一些事情判斷


```javascript=
const routes = [
  {
    path: '/posts',
    component: PostsLayout,
    children: [
      {
        path: 'new',
        component: PostsNew,
        // 只有經過身份驗證的用戶才能創建帖子
        meta: { requiresAuth: true }
      },
      {
        path: ':id',
        component: PostsDetail
        // 任何人都可以閱讀文章
        meta: { requiresAuth: false }
      }
    ]
  }
]

```


```javascript=
router.beforeEach((to, from) => {
  // 而不是去檢查每條路由記錄
  // to.matched.some(record => record.meta.requiresAuth)
  if (to.meta.requiresAuth && !auth.isLoggedIn()) {
    // 此路由需要授權，請檢查是否已登錄
    // 如果沒有，則重定向到登錄頁面
    return {
      path: '/login',
      // 保存我們所在的位置，以便以後再來
      query: { redirect: to.fullPath },
    }
  }
})
```


## 懶加載

原寫法
```javascript=
import About from './components/About.vue'

const routes = [
    { path: '/', component: Home },
    { name:'about', path: '/about', component: About },
```

新寫法

```javascript=
const About = import('./components/About.vue')

const routes = [
    { path: '/', component: Home },
    { name:'about', path: '/about', component: About },
```

簡單來說，變動只有第一行。
**2021-06-20目前 webpack才能用的樣子，Vite不能樣子**

## 獲取資料(重點)

當初我在想，載入資料不就寫在 `create` 就好了嗎?後來我發現，不能這樣，所以官方範例都用 `$watch` 去做監聽參數。


[API — Vue.js](https://cn.vuejs.org/v2/api/#vm-watch)

有兩個方式

```htmlembedded=
<template>
  <div class="post">
    <div v-if="loading" class="loading">Loading...</div>

    <div v-if="error" class="error">{{ error }}</div>

    <div v-if="post" class="content">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>

```


方式一

```javascript=
export default {
  data() {
    return {
      loading: false,
      post: null,
      error: null,
    }
  },
  created() {
    // watch 路由的參數，以便再次獲取數據
    this.$watch(
      () => this.$route.params,
      () => {
        this.fetchData()
      },
      // 組件創建完後獲取數據，
      // 此時 data 已經被 observed 了
      { immediate: true }
    )
  },
  methods: {
    fetchData() {
      this.error = this.post = null
      this.loading = true
      // replace `getPost` with your data fetching util / API wrapper
      // 用你的數據獲取 util 或 API 替換 `getPost`
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    },
  },
}

```

使用 randomuser 參照官方做個簡單範例

GIT: [6995135924b391a6cd9fe3f8564e3aa80051b036](https://github.com/malagege/vue-router-test/commit/6995135924b391a6cd9fe3f8564e3aa80051b036)

方式二

```javascript=
export default {
  data() {
    return {
      post: null,
      error: null,
    }
  },
  beforeRouteEnter(to, from, next) {
    getPost(to.params.id, (err, post) => {
      next(vm => vm.setData(err, post))
    })
  },
  // 路由改變前，組件就已經渲染完了
  // 邏輯稍稍不同
  async beforeRouteUpdate(to, from) {
//    this.post = null
//    try {
//      this.post = await getPost(to.params.id)
//    } catch (error) {
//      this.error = error.toString()
//    }
    getUser(to.params.id, (err, User) => {
      this.setData(err,User)
    })
  },
  methods: {
    setData (err, User) {
      if (err) {
        this.error = err.toString()
      } else {
        this.User = User
      }
    }
  }


```

官方忘記加了 setData method，回看 v3 版本文件有這個 method

GIT: [8b717a40aa0169fcd8a6fb8eaf3500f65119c428](https://github.com/malagege/vue-router-test/commit/8b717a40aa0169fcd8a6fb8eaf3500f65119c428)