---
title: 展望 Javascript 2016年的趋势和生态发展
categories: ['presentation']
---

> 本文翻译自[State of the Art JavaScript in 2016](https://medium.com/javascript-and-opinions/state-of-the-art-javascript-in-2016-ab67fc68eb0b)，加上了部分译者的观点。就像是隧道终点前的光明，JS生态的最佳实践不再剧烈变更着，现在关于需要学什么越来越明确了。本文就关于核心类库，状态管理，语言特性，构建工具，CSS预处理，API & HTTP 类库，测试工具，依赖管理等等前端开发的方方面面进行了展望和梳理，为你挑出这些最佳实践和面向未来的设计~

## 展望 Javascript 2016年的趋势和生态发展

那么，你要开始一个崭新的Javascript前端项目了，或者被之前老项目折腾半死，你也许并没有和改变进化步伐极快的社区生态保持技术实践的同步，或者你开始了，但是有大量的可选项不知道怎么选。React，Flux，Angular，Aurelia，Mocha，Jasmine，Jasmine，Babel，TypeScript，Flow。哦我的天呐这么多~ 为了让事件变得更简单，我们很多人正陷入一个陷阱：被我喜欢的XKCD漫画描述的很好：

![](media/14595750263914.jpg)

是的，好消息是现在JS生态开始慢下来了，好项目开始冒出。最佳实践爱你慢慢变得更清晰了。工程师开始在现有项目上构建自己的工程还是重新创造轮子。

作为起点，下面是我对现代化Web应用各个部分的个人选择。一些选择看起来会有些争议，我会在每个选择后附上我的基本推理判断。要注意的是，这些选择通常是我建立在我目前对社区的观察和我个人的经历。你的看法当然会有不同~

### 核心类库：React

![](media/14595750418996.jpg)

目前胜者很显然就是React（译者：你确定？！）

- 从顶到底都是组件，你的应用程序代码非常容易理解
- 学习曲线非常平缓，要知道罗列它所有关键的API都不会超过一页A4纸张。
- JSX非常棒，你可以用获得JavaScript编程语言的能力和工具链来描述页面
- 使用Flux和Redux这种单向数据流非常直白来表达业务逻辑
- React的社区生态圈非常不错，产出了很多高质量的开发工具如Redux（后续会讲到）
- 在React写的大型项目中，利用Flux管理起数据流/内部状态非常容易（而不像在双向数据绑定中， 例如Angular1.x/Knockout中
- 如果你需要类似于服务端渲染，那么选React没错了

现在不少全能的大型框架如Ember，Angular，它们承诺说帮你处理所有的事。但是在React生态中，尽管需要对组件做一些决定（哈这就是你为什么要阅读本文的原因啦），但是这方案更强壮。很多其他框架，譬如Angular2.0正在快速追赶React。

『选择React不仅是个技术上的选择，更多是个商业决定！』

- 额外奖励：一旦你要着手构建自己的移动应用，你会感谢ReactNative项目的

（很牵强哈，Angular社区的Ionic也是PC Web向移动端迁移的好选择。各自的2.0版本也相辅相成推进上）

### 应用生命周期：Redux

![](media/14595751610038.jpg)

PS: 以上不是它最终的logo

现在我们有了我们的视图和组件层，应用程序还需要管理数据状态和应用的生命周期。Redux也是毋容置疑的优胜者。

除了React，Facebook展示了名叫Flux的单向数据流的设计模式。Flux最早用来解决和简化应用的状态管理，但是随之而来，很多开发者提出了不少新的问题如如何存储数据状态和从哪发送Ajax请求。

为了解决这些问题，不少基于Flux模式之上的框架诞生了：Fluxible, Reflux, Alt, Flummox, Lux, Nuclear, Fluxxor 还有很多。

不过，这其中的类Flux的优雅实现最终赢得了社区的关注，它就是Redux。

最重要的是，学习Redux小菜一碟。它的作者，Dan Abranmov是一位非常棒的老师（他的教学视频非常易懂好学）。通过那些视频你很容易成为Redux的专家。我见见识到一组几乎没有任何React开发经验的工程师通过学习他的视频，在几周内就开发好能上线的React项目（代码质量也是顶级的）

Redux的生态系统和Redux本身一样优秀。从神乎其神的开发者工具到令人记忆深刻的reselect，Redux的社区是你最好的后盾。

不过有一点需要注意的是不要轻易的去尝试抽象Redux的项目模板。那些模板背后都是有意义有原因的。所以你尝试盲目修改前确保你已经使用过它和理解这样组织代码背后的原因。


### 语言：ES6配合Babel，先不加类型

![](media/14595751679057.jpg)

不用在用CoffeeScript了（译者哭了出来，公司13年大规模的使用它，至今后端JS项目80%都是它的身影）。因为它的大部分好的特性在ES6都有了（它是JavaScript的标准语言规范），而且CoffeeScript的工具很弱（如CoffeeLint），它的社区也在快速的衰落。

ES6是语言的标准。它的大部分特性在最新的主流浏览器中已经被实现，Babel是个可插拔ES6编译器。把它配置到使用合适的预设目标（preset target，如es2015浏览器，es2015-node5），就可以开动了。

那么关于类型了，TypeScript和FLow都给JavaScript提供了加上静态类型的功能，来配合编辑器提供更好的IDE支持和不需要测试就能捕获一些bug。不过我还是建议，先等等看，看看社区的进展和动向。

TypeScript做了很多工作让JavaScript写起来看起来非常像Java/C#，但它缺乏完善的现代化的类型系统特性如代数数据类型（它对你真正开始实操静态类型时是很重要的）。它也不像Flow那样很好的处理 nulls。

更新：TypeScript有union types也能覆盖很多用户开发场景。

Flow比起来似乎更强大，能捕获到更大范围的bug异常，但是比较难设置。在语言特性上，它比起Babel落后不少，在Windows上的支持也不好。

我要说些有争议性的话了：类型在前端开发中并没有我们想的那么重要（可能需要写一篇长文来讲述了）。所以就先等着类型系统变得更强健，目前先只使用Babel，同时关注下Flow~


### 格式和风格：ESlint配合Airbnb指南

![](media/14595751760900.jpg)

关于ESLint异议也不大。使用它的React插件和良好的es6的支持，几乎非常完美完成lint的工作。JSLint是过时了，ESLint这个软件可以单独完成原本需要 JSHint 和 JSCS 联合起来做的事。

你需要配置他用你的风格约定。我强烈建议你使用 Airbnb的风格指南，大部分可以被 ESLint airbnb config 来严格约束实现。如果你们团队会在代码风格上产生分歧和争超，那么拿出这份指南来终结所有的不服！它也不是完美的（因为完美的风格不存在），但保持统一一致的代码风格是要高度推荐的。

一旦你开始熟悉它，我建议你开启更多的规则。在编辑撰写代码时候越多的捕获不规范（配置你的编辑器IDE使用上这个ESLint插件），就会避免分歧和在决定费神，从而让你和团队更加高效！


### 依赖管理：仅考虑NPM，CommonJS 和ES6模块

![](media/14595751832101.jpg)

这一点很明确 - 就用NPM。所有东西，忘记之前的bower。类似与 Browserify 和 Webpack 的构建工具把npm的强大功能引到了web上。版本处理变得很简单，你也获得了node生态的大部分模块提供的功能。不过CSS的相关处理还是不够完美。

你可能会考虑的一件事：如何在开发服务器构建应用。不想Ruby社区的Bundler，npm 常使用通配符来指定版本号，导致你开始书写代码到最后部署时候版本号很可能已经变化了。使用 shrinkwrap 文件来锁定你的依赖（我建议使用 [Uber的 shrinkwrap](https://github.com/uber/npm-shrinkwrap) 来获得更见一致性的输入）。同时考虑使用利用类似于 [Sinopia](https://www.npmjs.com/package/sinopia) 来构建自己的私有npm服务器。

Babel可以把 ES6 模块语法编译到CommonJS。意味着你可面向未来的语法，和在使用构建工具（如Webpack 2.0）时获得它支持的一些静态代码分析工具如 [tree shaking](http://www.2ality.com/2015/12/webpack-tree-shaking.html) 的优势


### 构建工具：Webpack

![](media/14595751900659.jpg)

不想在你的页面文件中加入非常多的外链Script引用，那你就需要一个构建工具来打包你的依赖。如果你也需要允许npm包在浏览器运行工作的工具，那么Webpack就是你需要的。

一年前，关于这块还有不少选择。根据你的开发环境，你可以利用Rails的sprockets来解决类似问题。RequireJS，Browserify和Webpack都是以JavaScript基础的工具，现在RollupJS声称自己在ES6模块上处理的更好。

在一个个尝试使用后，我最终还是高度推荐Webpack：

- 它有自己独特的写法，不过即使是最少见的场景也能找到解决的方法
- 主流的模块格式都支持（如AMD，CommonJS，Globals写法）
- 它有处理有问题模块的能力（不是标准的模块写法
- 它能处理CSS文件
- 它有最完善的cache busting/hashing 系统（如果你需要把你的资源发布到CDN）
- 它内置热加载功能
- 它能加载几乎你需要的一切
- 它一套令人惊叹的优化列表

Webpack目前也是处理大型SPA应用项目的最好方案，利用它的代码切割（code splitting）和懒加载特性。

不过它的学习曲线很陡峭，但是一点你掌握了，你还是会认为非常值得因为它的强大功能。

那么Gulp或Grunt呢？ Webpack比起来最适合处理静态资源。所以他们开始可以用来跑一些其他的任务（但是也不推荐），现在更简单的方法是直接用上 [npm scripts](https://docs.npmjs.com/cli/run-script)


### 测试：Mocha + Chai + Sinon（但没那么简单）

![](media/14595751982308.jpg)

目前在 JavaScript 单元测试上，我们有众多选择，你选择任何一个都不会错太多。因为你开始做单元测试，你就走对一大步了。

一些选择包括了 Jasmine，Mocha，[Tape](https://github.com/substack/tape)，[AVA](https://github.com/sindresorhus/ava) 和 Jest。我知道我漏掉来一些，它们都有一些比其他做的好的地方。

我对一个测试框架的的选择标准是：

- 它应该可以在浏览器中运行从而方便调试
- 它应该运行的足够快
- 它可以很好的处理解决异步测试
- 它可以在命令行就能很方便的使用
- 它应该允许我使用任意断言库，而不会约束限制

第一个指标让AVA脱颖而出（因为它的确做的非常棒）和Jest（自动Mocking并不像它说的那么好，因为它太慢了）

选择Jasmine，Mocha或Tape都不会差太多。我倾向于 Chai 断言因为它拥有很多插件，Sinon's mocks to Jasmine's built in construct。Mocha的异步测试支持很棒（你不用在写类似于 done callback之类的）。[Chai as Promised](https://github.com/domenic/chai-as-promised) 也是很屌。我强烈建议你使用 [Dirty Chai](https://github.com/prodatakey/dirty-chai) 来避免一些让人头疼的问题。Webpack的 [mocha loader](https://github.com/webpack/mocha-loader) 让你编码时自动执行测试。

对于React而言，Airbnb的[Enzyme](https://github.com/airbnb/enzyme)和Teaspoon（不是Rails那个！）是不错的相关工具选择。

我非常喜欢Mocha的特性和支持情况。如果你喜欢一些最小主义的，读读这篇[关于tape的文章](https://medium.com/javascript-scene/why-i-use-tape-instead-of-mocha-so-should-you-6aa105d8eaf4)

PS：
Facebook在最近的文章中说，它们是如何扩展Jest的。可能对大部分人来说过于复杂了，如果你有那些资源，不关心是否在浏览器中跑测试，那么它就很适合你。

另外，很多人认为我对AVA太武断了。不要误会我，AVA的确很棒。但我有个标准：全浏览器支持。这样我们可以直接从任何浏览器去执行（来测试跨浏览器兼容性）同时要方便调试。如果你不介意那些，你可以使用非常棒的iron-node来debugging。


### 工具库：Lodash是绝对王者，但留意 Ramda

JavaScript不像Java或.NET上有很多强大的内置工具集。所以你可能需要引入一个。

Lodash，目前来说应该是杂七杂八都有的首选。同时它类似注入[懒求值](http://filimanjaro.com/blog/2014/introducing-lazy-evaluation/)这样的特性让它成为性能最高的选择之一。如果你不想用你就不要把它全部都导入，同时：lodash能让你仅仅引入哪些你需要的函数（这点尤其重要考虑到它现在变得越来越大）。随着4.x版本带来，它原生支持可选的函数式模式给那些函数式编程的极客们使用。来看看[怎么使用它](https://github.com/lodash/lodash/wiki/FP-Guide)

如果你真的很喜欢函数式编程，那么不管怎么样，留意下优秀的[Ramda](http://ramdajs.com/0.19.1/index.html)。如果你决定用它，你可能还是需要引入一些lodash函数（Ramda目前专注于数据处理和函数式构建），但这样你能在JavaScript中以非常友好方式获得函数式编程的强大功能


### Http请求：就只用fetch

许多React应用再也不需要jQuery了。除非你需要使用一些遗留的老旧的第三方组件（它依赖于jQuery），因为根本没必要。同时，意味着你需要一个类似于$.ajax的替代品。

为了保持简单，仅仅使用[fetch](https://fetch.spec.whatwg.org/)，它在Firefox和Chrome中内建支持。对于其他浏览器，你可能需要引入polyfill。我推荐使用[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) 来在服务器端在内覆盖了基础组件选择。

还有一些其他好的类库选择如[Axios](https://github.com/mzabriskie/axios)，但目前在fetch之上没有多余需求。

为了更多关于为什么Promise是重要的讨论，请看我们博客异步代码


### 样式：考虑CSS模块

这是个我觉得相对发展较慢的领域。Sass就是目前的选择，使用node-sass是你JavaScript项目的不错开始、。我仍然觉得离最终最好的方案还是有很多不足。缺乏引用导入（如仅仅是从文件导入变量和mixin，而不用重新定义选择器和它的样式规则）和原生的URL重写（使它在保持线上代码足够精简就很困难）。node-sass是一个C写的类库，所以要对应好你的Node版本。

LESS并不受此影响，不过它缺了很多Sass的特性功能。

PostCSS 看起来更有生命力，它允许你构建自己的CSS预处理器。我推荐你使用它，即使你已经有了你喜欢的预处理器如SASS，它类似于AutoPrefixer使得你不需要要导入类似于Bourbon这样的大型依赖。

有一点需要特殊注意的是，CSS Modules。它限制了CSS的层叠部分，使得我们可以定义更加明确的依赖，来避免冲突。你再也不用担心Class名称一致导致的覆盖，也不用特意为了避免它而添加额外的前缀。它和React也配合的很好。有个不足：css-loader和css modules一起使用会导致非常缓慢，如果你的样式数量不少，那么在它优化之前还是避免使用它吧。

如果要我现在从头开始一个新项目，那么我大概会使用PostCSS配合一些我喜欢的预编译的CSS类库。

不管你选择什么，我还是推荐你看下我这篇文章[CSS performance with Webpack](http://eng.localytics.com/faster-sass-builds-with-webpack/)，尤其你也在配套使用SASS。

### 前后同构：确保你真的需要它

Universal或Isometric的JavaScript代表着JavaScript写的代码可以被同时运行在客户端和服务器上。这需要用来在服务器端预先渲染页面来提升性能或SEO友好。感谢React，之前只有类似于Ebay或Facebook这样的巨型科技公司才能实施的方案，现在很多小的开发团队都能做到。不过它并不那么容易，它显著的加大了复杂性，限制了你类库和工具选择。

如果你在构建B2C的网站，类似于电商网站，那么你可能必须使用这个方案。但如果你是在做内部网站或者是B2B应用站点，那么这样的性能提升和SEO其实并不需要。所以和你的项目经理讨论下，看看是否有必要~

### API接口：暂时没有好方案

看起来每个开发者最后都会疑问那么关于API接口呢？很多人会直接想到[RESTful API](https://en.wikipedia.org/wiki/Representational_state_transfer)（因为太流行了），同时SOAP真的成为过去式了。同时现在也有不少其他标准如：[HATEOAS](https://en.wikipedia.org/wiki/HATEOAS), [JSON API](http://jsonapi.org/),[HAL](http://stateless.co/hal_specification.html),[GraphQL](https://facebook.github.io/react/blog/2015/05/01/graphql-introduction.html) 等

GraphQL 赋予客户端强大的能力（也是职责），允许它来实施任意的查询接口。结合Relay，它能为你处理客户端状态和缓存。在服务器端实施GraphQL看起来比较困难而且现有的文档大部分是针对Node.js的

网飞(NetFlix）的[Falcor](https://github.com/Netflix/falcor) 看起来它也能提供那些Relay和GraphQL提供的功能，但是对于服务器端的实现要求很低。但现在它仅仅是开发者预览版没有正式发布。

所有这些有名的标准规范有他们各种的奇怪之处。一些是过于复杂，一些只能处理读取没有覆盖到更新接口。一些又严重和REST走失。许多人选择构建它们自己的，但是最终也要解决它们设计上带来的问题。

我不认为现在有什么方案是个大满贯（完美的），但是下面是我对API应该有的功能的一些思考：

- 它应该是可预测的，你的API节点应该是遵循一致性规范的
- 它可以在一次就能拉取多个资源的数据（如果你的应用启动需要来回请求十几个接口来获得相关的资源数据，应用的性能应该是非常糟糕的
- 它可以覆盖除去读取，也包括更新的能力（很多方案仅仅提供读取的
- 它应该容易debug：譬如看看Chrome的开发者工具的network网络tab下就能审视发生了什么
- 它应该容易被消费，譬如可以简单的通过 fetch API 方式或者有相应的客户端类库支持如Relay

我没有找到能覆盖这些需求的方案。如果有，务必让我知道。
同事考虑，如果你实施一个标准化的RESTful资源路径时，使用Swagger来文档化我们的API。


### 客户端软件：Electron

Electron 是Atom这个优秀编辑器的背后基石。你可以用它来构建自己的应用程序。它的核心，就是一个特殊版本的Node可以来打开Chrome窗口来渲染GUI页面，并且能够获取到操作系统底层的API（不用借助浏览器的安全沙箱等措施）。你可以打包你的应用程序像其他的桌面客户端软件那样分发安装包，并且辅以自动更新机制。

这就是用来构建横跨OSX，WIndows和Linux这多个桌面环境的应用软件的最简单的方式，利用起上面提到的所有好用的工具。它也有不错的文档和活跃的社区支持。

你也许也听过nw.js（之前叫node-webkit）它年事已高，Electron比它更稳健，更容易上手使用。

来使用这个模板项目来开搞自己的Electron，React和热更新项目吧，你可以看看这些东西是怎么相互配合工作的~


### 向谁学习，从哪学起

社区有一些大牛，你可以在Twitter，Weibo，掘金上关注他们。
还有不少好的文章和链接。

如 [Removing user interface complexity, or why React is awesome](http://jlongster.com/Removing-User-Interface-Complexity,-or-Why-React-is-Awesome) 就是对了解React背后实现原理和原因的不错讲述。

### 如果你不需要，就别用它！

JavaScript生态区发展很快，欣欣向荣，在快速前进着。但是就像是隧道终点前的光明，最佳实践不在剧烈变更着。现在关于需要学什么越来越明确了。

最重要的是记住要保持简单，如无必要，勿增实体。

如果你的应用就是两三个页面，那你不需要用到Router。如果就一个页面，那么你也不用Redux，只需要用React自己管理状态就好。你的应用就是简单的CRUD程序，那么不需要用到Relay。你在学习ES6的语法，那么先别急着弄 Async/Await 或者装饰器。你准备开始学习React了，你不必要立马就去了解Hot Reload热更新和服务器端渲染。如果你刚刚使用上webpack，那么也别急着就来用代码切割（code splitting）和多代码块（multiple chunks）。如果你才开始Redux，那么也不用匆忙用Redux-Form或Redux-Sagas。

保持简单，一次学一样，你就不会像其他人一样抱怨JavaScript的破碎（Fatigue

### 我少了什么吗？

这就是我对目前JavaScript生态现状的看法和观点。如果你觉得我遗漏了那些类别的讨论，或者你认为我在一些选型上的决定不正确，你有更好的可以推荐。那么留言吧~
