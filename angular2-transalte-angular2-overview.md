---
title: 【Angular2】开发指南 2. 架构综述
---

{% raw %}

## 架构综述

Angular 2 是一个帮助我们用 Html 和 JavaScript 构建客户端程序的开发框架。 框架有几个相互配合的库组成，有些是核心库有些是可选的库。
<!-- more --> 
我们的应用程序由：
- 使用Angularized化的模板创作HTML模板
- 书写组件类来管理模板
- 利用服务添加应用逻辑
- 用Angular的启动器（boostrapper）来启动顶层根组件

启动后的Angular程序，在浏览器中展示应用内容，通过我们的提供的指令来响应用户的交互。

我们在高纬度通过俯视看这些坐标型的概念来获得全局认识，详细的讲解会在后续的章节逐步开展。
一个Angular2的应用程序一般有以下7个主要的部分组成：

![](https://raw.githubusercontent.com/gf-rd/blog/master/assets/angular2-developer-guides/2-overview/14527529748957.jpg)

这个系统框图展示了一般 Angular 2 应用程序的八个主要的组成部分

1. [模块](#module)
2. [组件](#component)
3. [模板](#template)
4. [元数据](#metadata)
5. [数据绑定](#databinding)
6. [服务](#service)
7. [指令](#directive)
8. [依赖注入](#dependencyinject)

> 该章节的代码示例可以在官网看到 - [live example](https://angular.io/resources/live-examples/architecture/ts/plnkr.html)



### <a name="module"> 模块
Angular 应用是模块化的。
通常我们的应用程序由多个模块组合而成。 一个典型的模块是内聚性良好来解决单一问题的代码块，它通常在代码中导出某些值，譬如一个的类。

> **模块是可选的**
> 我们高度推荐模块化的设计。TypeScript 对 ES2015 的模块语法有着非常好的支持，我们这章节也是默认采取模块语法来实现模块化的设计。这就是我们把模块列为基础构建块的原因了。
> 
> Angular 本身不需要模块化的设计也不要求那样的语法。如果你不想用当然可以不采用。每章节在你罗列出清晰的 `import` 和 `export` 语句后都可以替换掉。
> 
> 在 JavaScript track 中找到设置和代码组织的方法（在页面顶部的下拉列表框中），那就就可以看到将仅仅用老旧低版本的 JavaScript 和无模块系统的代码示例

我们遇到的第一个模块很可能就是导出的组件类的模块。组件是基础的Angular构建块，我们已经提到它很多次了。我们会在接下的部分详细讲解。目前，我们仅仅需要知道组件类也是一种我们可以从模块中导出的代码

大部分应用会有一个 `AppComponent`。一般来说， 我们在项目目录中发现名叫 `app.component.ts`的文件. 到该文件中我们会发现有如下的export语句:

app/app.component.ts (部分节选)
```js
export class AppComponent { }
```

export 语句告诉 TypeScript，该模块的 AppComponent 类是公共的（public）和可以在应用的其他模块中被访问到。

当我们需要引用 AppComponent 的时候，只需要像这样 import 就行：

```js
import {AppComponent} from './app.component';
```

import 语句告诉系统：可以在同级目录下名叫 app.component 的模块中获得 AppComponent. 模块名（或者模块Id）通常和没有扩展名的文件名是保持一致的。


#### 库模块(Library Modules)
一些模块可能是其他模块的库。 Angular 本身以名叫 barrels 库模块的集合形式提供。每个 Angular 的库其实是多个相关的私有模块的切面（facade）导出。

`angular/core` 库是主要的Angular库模块，它提供了我们应用开发所需的大部分内容。
当前还有一些一些重要的库模块，如 `angular2/common`， `angular2/router` 和 `angular2/http`。

> 在可以这[Modules，barrels和bundles](https://github.com/angular/angular/blob/master/modules/angular2/docs/bundles/overview.md)获得更多关于 Angular 组织和分发的模块。

我们以类似的方式，从Angular的库模块中导出我们需要的部分。例如，我们像这样从 angular/core 模块导入 Component 函数：

```js
import {Component} from 'angular2/core';
```

对比我们之前导入 AppComponent 的语法：

```js
import {AppComponent} from './app.component';
```

看到区别了吗？ 在第一个例子中， 当我们从 Angular 库模块中导入时，语句中的模块名angular/core 是没有路径前缀的('./xx')

当我们从我们自己的文件中导入的时候，我们在文件名前加上路径前缀。 在这个例子中，我们申明了相对路径形式(./)。 那就意味着模块的代码和导入它的文件在当前相同的目录下。我们可以通过这样的语法在导入在应用的目录结构中其他位置的模块文件。

> 我们使用 ECMAScript 2015 (ES2015) 的导入导出语法。 在[这里](http://www.2ality.com/2014/09/es6-modules-final.html)了解更多语法。
> 
> 在模块加载和导入背后的基础设施是重要的主题。但这是超出了介绍 Angular 的范畴外主题概览。所以我们集中精力在我们的Angular应用程序上，目前了解 import 和 export 就够用啦。

要记住的关键点：

- Angular应用是由模块构建成的。
- 模块导出某些部分 - 类，函数，值，来在其他模块中被导入
- 我们倾向于把我们的应用代码拆分成一系列的模块集合，每个模块完成好它负责的一件事。



### <a name="component"> 组件
一个组件控制着一部分我们称之为视图的屏幕内容。应用外围包含导航链接的壳，heroes 列表，hero 编辑器等，他们全是被组件component控制的视图。
我们在一个类中定义组件的应用逻辑来支持视图的展示，通过一系列的熟悉和方法的API定义，组件类来和view视图做交互

例如 HeroListComponent，拥有heros属性，它是通过一个服务返回heros数据的数组。同时，它也有 selectHero() 方法来设置 selectedHero 属性，当用户在列表中点击一个具体hero时。这样component类的如下：

```js
export class HeroListComponent {
  constructor(private _service: HeroService) { }
  
  heroes:Hero[];
  selectedHero:Hero;
  
  ngOnInt() {
    this.heroes = service.getHeroes();
  }
  
  selectHero(hero: Hero) { this.selectedHero = hero; }
}
```

当用户在应用程序中移动浏览时，Angular 会创建，更新，销毁组件。当然我们开发者可以通过 lifecycle hooks 钩子在组件的生命周期的关键时间点插入特定的处理

> 我们并未在示例代码中展示这些钩子，你们要记住：在后续的章节会详细讲解
> 
> 当然我们会疑问谁会调用组件的构造函数，谁提供那些服务参数。目前，我们只需要知道Angular会在我们需要的时候调用组件的构造函数和提供所需的`HeroService`



### <a name="template"> 模板
我们通过组件相应的模板来定义它的视图。模板就是以html的形式告诉Angular如何渲染组件
绝大部分的模板看起来很像html，除了部分特殊的语法。以下是我们的HeroList组件的模板

```html
<h2>Hero List</h2>
<div *ng-for="#hero of heroes" (click)="selectHero(hero)">
  {{hero.name}}
</div>
<hero-detail *ng-if="selectedHero" [hero]="selectedHero"></hero-detail>
```

`<h2>`和`<div>`的标签我们很熟悉了，但是*ng-for, {‌{hero.name}}, (click), [hero], 和 `<hero-detail>` 是什么鬼。
它们是[Angular的模板语法](https://github.com/gf-rd/blog/issues/18)的一部分， 我们在后续学习中会慢慢熟悉它们。

我们先把注意力集中在最后一行。 `<hero-detail>` 标签个代表HeroDetailCopment的自定义元素。

`HeroDetailComponent` 这个组件不同于我们之前学习的 `HeroListComponent`。 `HeroDetailComponent` （代码还没展示）展示了关于那个用户在 `HeroListComponent`中所选的具体英雄的相关事实，`HeroDetailComponent` 是 `HeroListComponent` 的子组件。
可以观察到，<hero-detail> 在那些我们熟悉的 HTML 元素中待得很好 - 我们可以也需要把我们的自定义组件和原生的HTML元素在同一个布局（html）中相互混合。通过这样的方式，我们组合复杂的组件树来构建出我们功能丰富的应用。

![](https://raw.githubusercontent.com/gf-rd/blog/master/assets/angular2-developer-guides/2-overview/14527575575368.jpg)



### <a name="metadata"> 元数据
元数据，告诉Angular如何处理一个类
我们现在回头看下之前定义的 HeroListComponent 的代码，我们发现它仅仅就是一个类，没有什么痕迹标明它是什么框架，或是Angular相关的。
事实上如果我们不告诉Angular它是一个组件的话，从代码上看它就仅仅是一个普通的类。
我们通过元数据告诉 Angular 这段代码是指 HeroListComponent 组件定义。
在 TypeScript 中最简单是通过装饰器来附加元数据信息。 这是 HeroListComponent 的元数据例子：

```js
@Component({
  selector:    'hero-list',
  templateUrl: 'app/hero-list.component.html',
  directives:  [HeroDetailComponent],
  providers:   [HeroService]
})
export class HeroesComponent { ... }
```

装饰器就是一个方法，所以装饰器一般也有配置参数。 @Component 装饰器就需要一个配置对象来给Angular提供如何创建和展示组件和它对应视图的信息。

我们在看下其他可能的 @Component 配置项：

- `selector`: css 选择器告诉 Angular 当它在父HTML中发现`<hero-list>`时创建和插入该组件实例。如果应用壳（或一个组件）的模板包含：

```html
<hero-list></hero-list>
```

Angular 在 这标签间插入一个 HeroListComponent 组件实例

- `templateUrl`: 我们上述看到组件的模板地址。
- `directives`: 是该模板所需的组件或指令的数组。正如我们在模板最后一行看到的，我们预期Angular会根据`<hero-detail>`标签所示的地方，来插入一个 `HeroDetailComponent` 。Angular 只会当我们在 `directives` 数组中提到 `HeroDetailComponent` 才会这么做。
- `providers`: 是组件所需的服务的依赖注入的提供器数组。正是通过这个属性angular知道该组件的构造器需要 `HeroService` 服务提供展示所需要的 heros 数据数组。

@Component 函数传入配置对象后，转换为附加在组件类定义上的元数据。Angular 在运行时通过发现元数据来知道如何做正确的事情。

模板，元数据和组件一起描述了视图。
随着我们不断了解Angular2，我们也逐步学习其他通过元数据装饰器来指导Angular行为的常用装饰器，如：`@Injectable`, `@Input`, `@Output`, `@RouterConfig` 等

记住：我们必须在代码中加入相应的元数据，这样Angular才知道如何处理和运行一些构建块。



### <a name="databinding"> 数据绑定
不借助框架，我们需要手动把数据值更新到Html的控件中和把用户的交互转变为值变化和方法调用。手工写这种推拉的更新逻辑非常费时，而且容易出错。正如熟练的jQuery程序员证明的那样是场噩梦！
Angular支持数据绑定，一种协调同步模板和组件其他部分的机制。通过在模板中加入binding绑定的标记，我们告诉Angular如何把两个部分组合在一起。
在Angular中，有四种形式的数据绑定语法。 每种形式都需要有个方向可以是指向DOM，或者是来自DOM，或者是双向的。正如图表展示的那样：
![](https://raw.githubusercontent.com/gf-rd/blog/master/assets/angular2-developer-guides/2-overview/14527566716026.jpg)

在 example 模板中， 我们展示了三种数据绑定的语法格式：

```html
<div ... >{{hero.name}}</div>
<hero-detail ... [hero]="selectedHero"></hero-detail>
<div ... (click)="selectHero(hero)"></div>
```

{{hero.name}}  这种是字符串内插，来在div 标签中显示组件的 hero.name 属性
[hero] 是属性绑定，把selectedHero 属性值从HeroListComponent 传递到 HeroDetailComponent 组件的 hero 属性下。
(click) 是事件绑定，当用户点击 hero的名字时候调用组件的selectHero方法

双向数据绑定是第四种重要的绑定形式，通过 ng-model 指令directive把属性和事件绑定结合起来。在HeroDetailComponent的模板中有个例子

```html
<input [(ng-model)]="hero.name">
```

Angular 每个 JavaScript 事件循环中一次性处理全部的数据绑定，从应用的组件树根部开始，深度优先。具体细节我们在后续章节有阐述，现在看起来数据绑定在模板和组件间和父子组件之间的通讯都起着非常重要的作用。



### <a name="directive"> 指令
我们的angular模板是动态的。 当angular渲染模板时，它把根据directive提供的指示来把模板变成DOM。

指令是配有指令元数据的类。 在typescript中，我们通过@Directive装饰器来给类添加元数据。

我们已经了解了一种形式的指令：Component。 组件就是一个拥有模板，通过@Componenter而不是@Directive 来提供模板导向的特性。

> 组件在技术层面上讲是一种指令。但组件对Angular应用来说非常独特和重要，所以在这篇技术概览中，我们把这两个概览分开来讲述。

还有其他两种，我们称为结构指令和属性attribute指令。

他们通常在元素标签中以属性形式出现，通过名称或更多通过一个赋值的目标或绑定

#### 结构指令
结构指令通过添加，删除和替换在DOM树中的元素来改变布局layouts。 看下自带的两个结构指令的用法：

```html
<div *ngFor="#hero of heroes"></div>
<hero-detail *ngIf="selectedHero"></hero-detail>
```

- `*ngFor` 告诉Angular为`heroes`列表中的每一个hero创建单独的div
- `*ngIf` 只有当选择的hero存在的时，才包含 HeroDetail 组件

#### 属性指令
属性指令来改变现有元素的展现和行为。在模板中他们看起来想是正常的HTML属性，因此得名。
如 ngModel 指令，用于实现数据的双向绑定，如：

```html
<input [(ngModel)]="hero.name">
```

Angular 自带了一小部分的其他组件，既包含了改变布局结构（如. ngSwitch）和修改DOM和组件某些方面的（如 ngStyle 和 ngClass）



### <a name="service"> 服务
Service 是个很宽泛的概念，来封装应用所需要的任意值，方法和特性。
虽然任何代码都可以成为service，但通常，一个服务有一个特定聚焦的目的。譬如：

- 日志服务
- 数据服务
- message bus消息总线
- 税收计算器
- 应用配置功能

Angular本身没有对service有特殊的定义，没有所谓的基础类，没有用于注册service的地方。但services对Angular也是至关重要的。

这里是用于打日志到浏览器控制台的服务类代码（app/logger.service.ts）：

```js
export class Logger {
  log(msg: any)   { console.log(msg); }
  error(msg: any) { console.error(msg); }
  warn(msg: any)  { console.warn(msg); }
}
```

这里展示用于拉取英雄数据并在Promise中返回数据的HeroService服务。该服务依赖于 LoggerSevice 和另一个用于和服务器交互沟通这些底层事物的 BackendService。

```js
export class HeroService {
  constructor(
    private _backend: BackendService,
    private _logger: Logger) { }

  private _heroes:Hero[] = [];

  getHeroes() {
    this._backend.getAll(Hero).then( (heroes:Hero[]) => {
      this._logger.log(`Fetched ${heroes.length} heroes.`);
      this._heroes.push(...heroes); // fill cache
    });
    return this._heroes;
  }
}
```

服务在Angular应用中也是无处不在的。
我们的组件是服务的使用消费大户。我们通常保持组件类组件精简轻量，它本身不去从服务器拉取数据，不做用户数输入的验证，不直接把日志信息达到console中。他们把这些任务委派给一个服务。

一个好的组件拥有属性和方法来用于数据绑定，同时把一些通用的业务委托给服务。

Angular 不强制约束限制这样的原则，即使你可能写一个3k多行的大杂烩的巨型组件。

但是它帮助我们实施时遵守这样的原则，譬如通过依赖注入让那些处理应用业务逻辑的services可以方便的被组件使用。



### <a name="dependencyinject"> 依赖注入
依赖注入是类在创建新的实例时提供它所需的全部依赖的一种声明方式。绝大部分的依赖是服务。angular通过依赖注入机制给component提供它们所需要的服务。
在 TypeScript 中，Angular 通过查看组件的构建函数的参数获得组件所需要的services，譬如 HeroListComponent 的构建函数需要 HeroService，代码如下：

```js
constructor(service: HeroService) {
  this.heroes = service.getHeroes();
}
```
当Angular创建该组件时，它首先通过向`Injector`请求所需要的service实例. 
Injector是维持存放着它创建的各服务实例的容器
Injector 通过 `Provider` 来创建新的服务实例
Provider 是用来创建service的代码方法

我们可以在应用组件树的任意的层级注册providers，通常我们在启动bootstrap应用的根组件出注册，这样整个应用的其他地方都可以用同一个服务实例。

```js
bootstrap(AppComponent, [
  BackendService, HeroService, Logger
]);
```

同样，我们可以在特定组件层级来注册：通过annotation语法

```js
bootstrap(AppComponent, [
  BackendService, HeroService, Logger
]);
```

更详细的依赖注入请见后续的章节一探究竟~


### 小结
通过以上的介绍，我们基本了解了构建 Angular 应用的八个主要的构建块

1. [模块](#module)
2. [组件](#component)
3. [模板](#template)
4. [元数据](#metadata)
5. [数据绑定](#databinding)
6. [服务](#service)
7. [指令](#directive)
8. [依赖注入](#dependencyinject)

这是 Angular 应用中任何其他部分的基石，足够我们开始新的Angular2项目了。尽管它并没有包含我们所需知道的一切。


### 其他部分
以下是一份简短的按照字母表顺序排序的其他 Angular 的特性和服务. 绝大部分会在后续的章节中有进一步的讲解和使用。

#### 动画
面向未来的动画库，使得开发者可以很容易的给组件行为加动画，而不需要过多了解动画技术或css的知识

#### 启动
用来配置和启动应用程序的根组件的方法

#### 改变监测
Angular 内部如何知道组件属性改变和何时去更新页面显示。 Angular如合理利用 zones 机制去拦截异步行为和实施它的脏检查策略

#### 组件路由
利用该Service，用户像浏览器通过urls跳转一样，在多屏应用中浏览跳转切换页面。

#### 事件
DOM触发事件，同样组件和服务也可以触发。Angular 提供了用于发布和订阅事件的机制，该机制包括了[RxJS 观察者提议](https://github.com/zenparsing/es-observable)的实现

#### 表单
通过HTML为基础的验证和脏检查，支持复杂数据条目的场景

#### HTTP
通过 Angular HTTP Client客户端，和服务器通信来拉取数据，提交保存数据和调用服务器端的方法。

#### 生命周期钩子
通过实现生命周期钩子的接口，我们开发者可以在组件生命周期的关键时刻（如从创建到销毁）插入自己的行为

#### 管道
用来转化格式化数据来显示的服务。在模板层使用pipes管道来提升用户体验，譬如，下方展示了 currency 现金的管道表达式：`price | currency: 'USD':true` 来把价格为42.33格式化为`$42.33`

#### 测试
Angular提供的测试库可以和框架本身来交互，从而可以方便的给我们的应用程序做单元测试



>### 下一章
> [3. 数据展示](https://github.com/gf-rd/blog/issues/19)


{% endraw %}

