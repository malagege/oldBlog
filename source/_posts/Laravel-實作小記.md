---
title: Laravel 實作小記
date: 2019-07-16 19:15:27
tags: [laravel]
categories: Laravel
---

由於最近看 Laravel 都沒有用在實戰上面
想藉由這個機會時做看看
不寫還真的有些不習慣
特別紀錄一下

--尚未整理完--

<!--more-->

## Laravel 新增檔案起手勢

### 建立一個 Model

--migration 順便產生 migrate 檔案

**Table 取的名會是複數**
**Table 取的名會是複數**
**Table 取的名會是複數**
獎如是要自己新增的要注意!!!

```bash
php artisan make:model Event --migration
php artisan make:model Timer --migration
```

### 建立一個 Controller

```
php artisan make:controller CustomersController
php artisan make:controller CustomersController --model=Customer
```

`--migration` 我以為能順便新增 migrate
但是不行!!!

### 三個願望一次滿足

新建東西，想把所有 Controller 建立出來要怎麼用?
[Laravel 5.4 create model, controller and migration in single artisan command - Stack Overflow](https://stackoverflow.com/questions/43187735/laravel-5-4-create-model-controller-and-migration-in-single-artisan-command)

```
php artisan make:model Todo -mcr
```

-m, --migration Create a new migration file for the model.
-c, --controller Create a new controller for the model.
-r, --resource Indicates if the generated controller should be a resource controller
或者

```
php artisan make:model Todo -a
```

-a, --all Generate a migration, factory, and resource controller for the model

## Model 要注意的事情

### fillable

每次新增 Model 完成，但通常會忘記填這個東西
仔細看這個，這個變數是防止 input 帶入 model 裡面
會被塞入奇奇怪怪的值
某種意義來說就是安全性!!

### 關聯

### hasMany <=> belongTo

hasMany

```
    public function comments()
    {
        return $this->hasMany('App\Comment');
    }
```

```
    public function post()
    {
        return $this->belongsTo('App\Post');
    }
```

[Laravel 多張 table 使用 ORM 查詢 (遠層一對多關聯) – Bryce'S Note](http://blog.twbryce.com/laravel-%E5%A4%9A%E5%BC%B5table%E4%BD%BF%E7%94%A8orm%E6%9F%A5%E8%A9%A2-%E9%81%A0%E5%B1%A4%E4%B8%80%E5%B0%8D%E5%A4%9A%E9%97%9C%E8%81%AF/)

### Many to Many

我一直以為 X 對 X 就是講兩個 table，但是多對多簡單來講就是要用`3個 table`
而中介 table 命名為`aaa_bbb`(照英文順序命名，如:~~bbb_aaa~~會有問題，但可透過 belongToMany(xxxx,'bbb_aaa'))

簡單說一下，Many to Many 實作使用 3 個 table 但是只會寫 2 個 table(但 pivot 可自訂 class)，詳細官網一下，但通常應該不用寫
使用上 attach,detach,sync 控制 pivot 內容
pivot 通常不會建立 `id` 欄位
當然要設定也是能做到

## ORM with 預載入

只能用再 X 對多

#### Event()->timers() 出現找不到 function 問題

`BadMethodCallException with message 'Call to undefined method App/Event::timers()'`
哪尼我照官方範例新增一個 Project 怎麼會錯呢!!!
怎麼 Google 都找不到問題，最後我發現是`protected`關係，要用`public`
為什麼呢? `App\Event::find(1)` 沒有 new 出物件
所以`protected`的`timers()`就呼叫不到!!!
這是很正常的
**所以要用`public`!!!**
**所以要用`public`!!!**
**所以要用`public`!!!**
耗費一小時在查這個 orz

### Model::find(20) 實現原理

這篇寫的很清楚
[深入理解 laravel 中的 Eloquent ORM、查询构建器的核心原理 - 简书](https://www.jianshu.com/p/026c6cd2767f)

### 命名建議

## Form 表單這件事

我覺得網頁這個要熟悉
才能掌握網頁的程式
但是實作才了解 Laravel 我還不太了解

### create 和 edit 拆開寫兩個 View

一開始實作我覺得應該有方法讓兩個 view 合併
我從書上 view 有一個 `old()` 函式可以呼叫 redirect->with 出來東西
但是 view 變數沒抓到(未定義)的時候，可能程式錯誤無法顯示問題

再繼續寫，我發現 from 表單我使用`route`定義的時候
我發現一件事情，action 導去路徑不一樣
`@method('POST')` 和 `@method('POST')` 不一致
就算這些可以用邏輯去判斷網址
但我覺得會越來越亂
為何不如就分開兩個 view

其實表單重複的部分
我覺得可以用`@include`帶入
不過我等熟悉看如何做到

## contoller view

```php
        return view('page/week1/list');
        return view('page.week1.show')->with('event', Event::find($id));
        return view('page.week1.update', ['event' => $event]);
```

可以用多個帶值到 View 裡面
可算說是非常方便

## 驗證起手勢

```php
        $input = Input::all();

        $validator = Validator::make($input, [
            'name' => 'required|max:100',
        ]);

        if ($validator->fails()) {
            return redirect()->route('week1.edit', ['id' => $id])
                ->withErrors($validator)
                ->withInput();
        }

```

尚未了解事情

```php
        // 非正確的時候會導入到首頁，目前找不到修改對應首頁方法，建議用Validator::make
        $validatedData = $request->validate([
            'name' => 'required|max:1',
        ]);
```

### error 顯示方法

redirect()->withErrors
可以抓取到

```html
@if ($errors->any())
<div class="alert alert-danger">
  <ul>
    @foreach ($errors->all() as $error)
    <li>{{ $error }}</li>
    @endforeach
  </ul>
</div>
```

> 正如前面所提到的，Laravel 會自動把使用者重導到先前的位置。另外，所有的驗證錯誤會被自動快閃至 session。
> 同樣的，注意我們不需要在 GET 路由明確的綁定錯誤訊息至視圖。這是因為 Laravel 會自動檢查 session 內的錯誤資料，如果錯誤存在的話，會自動綁定這些錯誤訊息到視圖。所以，請注意到 $errors 變數在每次請求的所有視圖中都將可以使用，讓你可以方便假設 $errors 變數已被定義且可以安全地使用。\$errors 變數是 Illuminate\Support\MessageBag 的實例。有關此物件的詳細資訊，請查閱它的文件。

### success 方法

內建沒有 WithSuccess
但還是可以塞自己想要的資料

```php
        return redirect()->route('week1.edit', ['id' => $id])
            ->with('success', '修改成功');
```

```html
@endif @if (session()->has('succes'))
<div class="alert alert-primary">
  <ul>
    <li>{{ session()->get('succes') }}</li>
  </ul>
</div>
@endif
```
**上面也可以用`@if(isset($success))`**
注意with 除了寫 session 他也可以當view 變數名稱

## Laravel 有關 with function 那些事情

一直看書看到有很多地方都有用到 with
不仔細看，我還以為他們都是一樣的東西
但是用在不同地方還是不一樣

### orm with

之前我有小記，但我沒有用，有空來小記
[總結要點：使用 Laravel 開發工具，ORM 查詢的 N + 1 問題 | Laravel China 社區 - 高品質的 Laravel 開發者社區](https://learnku.com/laravel/t/7778/summary-using-the-laravel-development-tool-the-n-1-problem-of-the-orm-query)

### view with

這個等於 view()第二個參數帶到 blade 去

[laravel5-example/resources/views/back/blog at master · bestmomo/laravel5-example](https://github.com/bestmomo/laravel5-example/tree/master/resources/views/back/blog)

View 資料夾要如何分?
目前我只想到 layout ,page
但官方沒有限制
很多 Project 都不時一樣

[laravel5-example/resources/views/back/blog at master · bestmomo/laravel5-example](https://github.com/bestmomo/laravel5-example/tree/master/resources/views/back/blog)

### redirect with

一直以為這個跟 view with 一樣
但是看一下原始碼
他是存在 session(flashdatg 下一個頁面就會清除) ，可以用 old 快速呼叫
原本我以為可以用 create 和 edit 共用同一個 view
但發現難度太高，因為 old('name',\$event->name)
但是在 create 頁面就會掛掉
也許我可以在 create 做一個`空event`?
有空試試看

withErrors 和 withInput 一樣的道理

## action 和 route 選擇

```php
    {{ action('EventController@edit', ['id'=>1]) }}
    {{ route('week1.edit', ['id'=>1]) }}
```

我選擇用 route
打很少字，就算之後要換檔名不用修改很多檔案裡面的內容
可以算是非常方便

## 用 Laravel Mix 記得加 meta csrf

```html
<meta name="csrf-token" content="{{ csrf_token() }}" />
```

Console.log 好心提醒
CSRF token not found: https://laravel.com/docs/csrf#csrf-x-csrf-token

## Laravel 5.x 移除 html helper

不知道為什麼超多人還是有用
[laravel 5 Form 和 HTML 的使用 - 袁超 - SegmentFault 思否](https://segmentfault.com/a/1190000002605467)

## DB(ORM) 建立交易

需要抓到外面的變數可以用 use

```php
        DB::transaction(function () use ($input, &$is_save) {
            $event = new Event($input);
            $event->save();
            $event->sort = $event->id;
            $is_save = $event->save();
        });

```

[鎖定資料 Lock · Laravel 5 學習筆記](https://kejyuntw.gitbooks.io/laravel-5-learning-notes/database/model/database-model-eloquent-lock.html)

## 服務容器

- [从 1 行代码开始，带你系统性地理解 Service Container 核心概念 | Laravel China 社区](https://learnku.com/laravel/t/3361/starting-with-the-1-line-of-code-with-a-systematic-understanding-of-the-core-concepts-of-service-container)
- [从 1 行代码开始，带你系统性地理解 Service Container 核心概念 | Laravel China 社区](https://learnku.com/laravel/t/3361/starting-with-the-1-line-of-code-with-a-systematic-understanding-of-the-core-concepts-of-service-container#%E4%B8%80%E4%B8%AA%E5%9F%BA%E7%A1%80%E7%89%88%E7%9A%84-IOC-Container)
- [Laravel 学习笔记 —— 神奇的服务容器 | Laravel China 社区](https://learnku.com/articles/789/laravel-learning-notes-the-magic-of-the-service-container)

以上兩篇我看完小記的觀點，不一定正確，建議還是看裡面的內容。
我覺得這幾篇講的名詞都很抽象，所以不是很好懂(但是裡面這幾篇算是我找得最清楚的)
先講講我怎麼看裡面容器
~~我當初以為所有類別都是容器~~但其實容器應該就是指`Container::getInstance`這一段類別

更深入了解 Ioc 和 DI 意思
我覺得可以看下面這邊
[控制反轉 (IoC) 與 依賴注入 (DI) - NotFalse 技術客](https://notfalse.net/3/ioc-di)

感想
看完有種想把所有東西都塞到容器裡面?
但我後來想想應該不需要
我覺得容器有點像兩個 class 工通橋梁(如 Laravel app())
再透過 Facade 或 helper function 更加方便
同時兩個類別可以呼叫不同 class bind 後的實例

[Laravel 如何實現依賴性注入？ - 技術保鮮盒 - Medium](https://medium.com/mr-efacani-teatime/laravel%E5%A6%82%E4%BD%95%E5%AF%A6%E7%8F%BE%E4%BE%9D%E8%B3%B4%E6%80%A7%E6%B3%A8%E5%85%A5-d760c8e5abde)

### Repository

- [在 Laravel 5 中使用 Repository 模式实现业务逻辑和数据访问的分离 - Laravel 学院](https://laravelacademy.org/post/3063.html)

- [为什么你应该使用 Repository - Laravel - SegmentFault 思否](https://segmentfault.com/a/1190000003488038)

- [如何使用 Repository 模式 · laravel 学习 · 看云](https://www.kancloud.cn/curder/laravel/408484)

## factory /seeder

[Laravel - Seeding Many-to-Many Relationship - Stack Overflow](https://stackoverflow.com/questions/45269146/laravel-seeding-many-to-many-relationship)

## form checkbox

[php - laravel 怎么接收和保存一组 checkbox 到数据库？ - SegmentFault 思否](https://segmentfault.com/q/1010000005107466)

## 控制器依賴注入原理

- [自訂隱晦路由器參數查詢 - Yish - Medium](https://medium.com/yish/%E8%87%AA%E8%A8%82%E9%9A%B1%E6%99%A6%E8%B7%AF%E7%94%B1%E5%99%A8%E5%8F%83%E6%95%B8%E6%9F%A5%E8%A9%A2-b4ce73d4df6a)
- [关于 laravel5.5 控制器方法参数依赖注入原理深度解析及问题修复 - 程序人生 ♨︎ - 博客园](https://www.cnblogs.com/springwind2006/p/7780524.html)

## 授權 GATE & Policy

[laravel5.5 授权系统 - archer-wong - 博客园](https://www.cnblogs.com/redirect/p/8658740.html#%E9%80%9A%E8%BF%87%E7%94%A8%E6%88%B7%E6%A8%A1%E5%9E%8B)

## Redis

Laravel 6.2 使用 Redis::get 會遇到
```
Please remove or rename the Redis facade alias in your "app" configuration file in order to avoid collision with the PHP Redis extension.
```

使用 `Cache::put` 看似能用，但預設 env 是 file，切換到 redis，還是遇到一樣的問題
看似 Homestead 預設沒安裝 redis 套件
phpredis 需要改裡面程式內容
這邊有兩個做法

1.  改成用 predis 方法(不推薦)

composer require predis/predis

> 1 Replace in config\app.php
> aliasess:
> 'Redis' => Illuminate\Support\Facades\Redis::class,
> for:
> 'RedisManager' => Illuminate\Support\Facades\Redis::class,
> 
> 2 Replace in config\database.php
> 
> 'redis' => [
>
>    'client' => env('REDIS_CLIENT', 'predis'), //default phpredis
>    ...

參考來源:https://github.com/laravel/horizon/issues/659#issuecomment-528569052

2.  改成用 phpredis 方法

Homestead 預設沒有安裝 php redis 套件，所以需要手動安裝
不知道新版的 Homestead 會不會預設安裝?
可參考:[Homestead 安装 PHP Redis 扩展 | Laravel China 社区](https://learnku.com/articles/33412) {% asset_link web1.png 備份圖 %}

相關資料:
[Homestead 安装 PHP Redis 扩展 - 菜园子 - SegmentFault 思否](https://segmentfault.com/a/1190000016606114)
[Homestead 环境没有 phpize 怎么安装 Redis 扩展？？？ | Laravel China 社区](https://learnku.com/laravel/t/14226/homestead-environment-without-phpize-how-to-install-redis-extension)
[Laravel 6 使用 Redis 注意事项 | Laravel China 社区](https://learnku.com/laravel/t/33538)

## Laravel Help

https://stackoverflow.com/questions/58163406/after-upgrading-laravel-from-5-6-to-6-0-call-to-undefined-str-random-function

## Laravel Router 多人維護最佳方式

[Laravel 分割 routes.php 路由文件的最佳方式 | Laravel China 社区](https://learnku.com/laravel/t/2484/the-best-way-to-split-routesphp-laravel-routing-file)

## Laravel blade Javascript 設計
[Laravel – 設計一個好的 Blade Template, 使用 @parent | 不怕就是強](https://dinos80152.wordpress.com/2015/08/04/laravel-%e8%a8%ad%e8%a8%88%e4%b8%80%e5%80%8b%e5%a5%bd%e7%9a%84-blade-template-%e4%bd%bf%e7%94%a8-parent/)

一般設計
[Jigsaw – Static Sites for Laravel Developers](https://jigsaw.tighten.co/docs/content-blade/)

## Laravel 6 安裝 Bootstrap 6

composer require laravel/ui --dev

```
// Generate basic scaffolding...
php artisan ui bootstrap
php artisan ui vue
php artisan ui react

// Generate login / registration scaffolding...
php artisan ui bootstrap --auth
php artisan ui vue --auth
php artisan ui react --auth
```
## blade
### 使用 Laravel Navbar 做 selected 設定

```html
<ul class="nav nav-second-level">
  <li class="{{ Request::segment(1) === 'programs' ? 'active' : null }}">
      <a href="{{ url('programs' )}}" ></i> Programs</a>
  </li>
  <li class="{{ Request::segment(1) === 'beneficiaries' ? 'active' : null }}">
      <a href="{{url('beneficiaries')}}"> Beneficiaries</a>
  </li>
  <li class="{{ Request::segment(1) === 'indicators' ? 'active' : null }}">
      <a href="{{url('indicators')}}"> Indicators</a>
  </li>
</ul>
```
or
```html
  <div class="container-fluid">
    <div class="navbar-header">
      <a class="navbar-brand" href="#">WebSiteName</a>
    </div>
    <ul class="nav navbar-nav">
      <li class="{{Request::is('/')?'active':''}}"><a href="{{url('/')}}">Home</a></li>
      <li class="{{Request::is('/page1')?'active':''}}"><a href="{{url('/page1')}}">Page 1</a></li>
      <li class="{{Request::is('/page2')?'active':''}}"><a href="{{url('/page2')}}">Page 2</a></li>
      <li class="{{Request::is('/page3')?'active':''}}"><a href="{{url('/page3')}}">Page 3</a></li>
    </ul>
  </div>
</nav>
```
- [php - Setting Bootstrap navbar active class in Laravel 5 - Stack Overflow](https://stackoverflow.com/questions/29837555/setting-bootstrap-navbar-active-class-in-laravel-5)
- [how to set bootstrap navbar class active - Scoopism](https://www.scoopism.com/bootstrap/how-to-set-bootstrap-navbar-class-active/)

### blade section/show


> 2. 使用 @section ('yourSectionName').....@show。子子孫孫都使用 @section ('contents')......@endsection 去繼承實現。

[關於 @section...@show;@section....@endsection 的用法分析 | Laravel China 社區](https://learnku.com/laravel/t/13853/an-analysis-of-the-usage-of-at-section-at-show-at-section-at-endsection)

## 其他

- [php - prepare 与 sql 的开销？ - SegmentFault 思否](https://segmentfault.com/q/1010000009649587/)
- [使用 batch insert 解决 MySQL 的 insert 吞吐量问题 - billy 鹏 - 博客园](https://www.cnblogs.com/billyxp/p/3631242.html)
  [php - Laravel Eloquent compare date from datetime field - Stack Overflow](https://stackoverflow.com/questions/25139948/laravel-eloquent-compare-date-from-datetime-field)
- [php 下 foreach()错误提示 Warning: Invalid argument supplied for foreach()\_一聚教程网](http://www.111cn.net/phper/php/58561.htm)
- [php 出现 Warning: A non-numeric value encountered 问题的原因及解决方法 - 傲雪星枫 - CSDN 博客](https://blog.csdn.net/fdipzone/article/details/78659784)
  pjax

### CDN

[刷新缓存的方法 - 七牛开发者中心](https://developer.qiniu.com/fusion/kb/1325/refresh-the-cache-and-the-effect-of-time)

- [app 接口设计 token 过期怎么使用\_百度知道](https://zhidao.baidu.com/question/1577731103328450940.html)
- [php ini - setting max_input_vars PHP.ini directive using ini_set - Stack Overflow](https://stackoverflow.com/questions/9973555/setting-max-input-vars-php-ini-directive-using-ini-set)
- [Laravel Migration、Model、DB 物件 相關操作 (濃縮) - La Tech.](https://www.latech.tw/2017/02/laravel-migrationmodeldb.html)
- [Redis 禁用 FLUSHALL FLUSHDB KEYS 命令 - 星瑞 - 博客园](https://www.cnblogs.com/gongxr/p/9522698.html)
- [Docker 使用 cron 定时任务 - 歪麦博客](https://www.awaimai.com/2615.html)
- [Laravel 模型关联 attach,save,sync 方法参数类型验证 - Coder_Chang 的博客 - CSDN 博客](https://blog.csdn.net/Coder_Chang/article/details/78815589)
- [associate 的意义？ | Laravel China 社区](https://learnku.com/laravel/t/17375)
- [Laravel\_使用 Passport 寫 API - 總有一天我要當超人](https://g8787856.github.io/2018/06/30/laravel-passport/)

[php - How to save in a polymorphic relationship in Laravel? - Stack Overflow](https://stackoverflow.com/questions/42950104/how-to-save-in-a-polymorphic-relationship-in-laravel)
[save() on polymorphic relation don't work](https://laracasts.com/discuss/channels/eloquent/save-on-polymorphic-relation-dont-work)

[模型关联 | 《Laravel Nova 中文文档》](https://learnku.com/docs/nova/1.0/model-association/2191#searchable-relations)
[[5.1] Saving morphTo relation. · Issue #11918 · laravel/framework](https://github.com/laravel/framework/issues/11918)
[Eloquent 关系图例（译） | Laravel China 社区](https://learnku.com/articles/6874/the-legend-of-eloquent-relations)
[laravel Carbon 函数 - 醒来明月 - CSDN 博客](https://blog.csdn.net/lbwo001/article/details/53063867)
[如何使用 K8S 自動化定期 CronJob 抓網路公開資料 | TechBridge 技術共筆部落格](https://blog.techbridge.cc/2019/06/29/how-to-build-kubernetes-k8s-cronjob-crawler/)
[Kubernetes 中的 Job 和 CronJob，批量任务执行 - mark's technic world - CSDN 博客](https://blog.csdn.net/hxpjava1/article/details/79815015)
[onlinereadbook/bookdocker: 線上 docker 讀書會](https://github.com/onlinereadbook/bookdocker)
[Repository Pattern and Service Layer – 佛祖球球](https://blog.johnsonlu.org/repository-pattern-and-service-layer/)
[Laravel、Ruby on Rails、Django ... 使用 Active Record Pattern 框架的最大問題：到底該把 save 寫在哪裡？ – Devs.tw](https://devs.tw/post/328)
[批評 Active Record 的13個論點：最好用也最危險的 Anti-pattern | 轉個彎日誌](http://blog.turn.tw/?p=2992)
[(1) Laravel 台灣](https://www.facebook.com/groups/laravel.tw/permalink/2899482063454157/)