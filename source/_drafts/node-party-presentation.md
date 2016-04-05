
### 自我介绍

- 豌豆荚 - WebApp
- 广发证券 - Hybrid应用 && Node.js
- Blog （一些技术上的思考和分享）

```note
前几位的分享都特别棒，在今天的活动的尾声我给大家带来的topics是讲述我们广发证券这样一个传统券商在新技术Nodejs的一些使用尝试的经验教训的。

先来个简单的自我介绍：我毕业前在百度实现做前端开发的一些工作，临近毕业突然觉醒想去创业公司去试试就到了豌豆荚一直从事公司的商品产品如开发者中心，集成了应用上传，游戏联系，广告投放管理和IAS接入，openAPI等等。去年开始从北京到深圳（原因你们都懂得），发现这边真是除了贵腾讯好像没有什么又大又好公司了，偶然的机会再拉钩上看到酷炫的xxx，一下子就被吸引了（虽然xxxx
```

### Agenda

agenda是：（一般是从上到下，从云到node的讲解

* node.js 在广发的位置
* 微服务和 node
* node koa2
* node.js 一般
* more than node.js web 开发常见问题


### 为什么我们这么玩 - 广发

#### How - 我们是怎么做的

- 金融创新
- 技术前沿（bleeding - edge）

```note
最早运用这些技术框架开发复杂运用的公司了（金融？
和大家想的一样，为什么一个传统券商，会在技术上这么有追求呢，你们的IT产品和需求不都是外包的吗？
那你们为什么还放出这样的广告『证券行业创新高涨，国际化进程中，投行等』
```


#### Why - 为什么这样的技术选型

- 吸引爱玩技术的你们
- 解决问题弯道超车
- 形成学习型组织

```note
为什么这么做了，激进的采用这样的方式，首先业务上（我是较高复杂度的譬如购买一个理财产品很多逻辑的判断和，流量上的倒不是很大，但业务上流动的钱到时候百千万到亿的~，其次我们推崇的微服务就允许让我们xxx（因为它xxx
最终还是招到人：
3点

这里就引入了我们今天的主角：Node.js，我们可能是金融行业乃至是互联网用这些技术开发复杂应用的团队了。 
```


#### 广发的开源

- ES6
- Angular2
- 更多见Blog，我们的技术分享

```note
所以从去年开始，开源上的做了一些工作：

广告这么长，背景，接下来我们切入进来了正题：
```


### Node.js 在广发的定位：

#### 技术全景图（业务位置）

#### 微服务改造（聚合和原子）

```note
首先看下我们的全景图xxx，
从泛终端作为入口，到我们的云端edge，对接micro services，后面有中间件和大数据backup，最终对标交易平台。

在我们的云端edge就大量使用着node。js来做它适合做的事情，甚至一些对性能不那么苛刻的微服务也有js来构建的（如果解决好类型等开发复杂应用稳定性

可以看到我们的架构中（接入层和微服务，原子化，场景化聚合）
（性能速度损耗，但是用户不care，可能被传统的金融银行等『惯坏』了，关于钱财的还是别轻飘的好
```


### Node.js 和 API Server


#### RESTful

- Optional-Field
- Filter & Sort
- Pagination
- Bulk Inserts
- Embedded & Sub Resource


```note

几层的RESTful成熟度模型

Hekru 提供的指南。

/people?where={"lastname": "Doe"}
/people?sort=city,-lastname
?sort=[("lastname", -1)] - mongodb style

/people?projection={"lastname": 1, "born": 1}

/comment/?embedded={"author": 0}

$ curl -d '[{"firstname": "barack", "lastname": "obama"}, {"firstname": "mitt", "lastname": "romney"}]' -H 'Content-Type: application/json' http://eve-demo.herokuapp.com/people

看起来每个开发者最后都会疑问那么关于API接口呢？很多人会直接想到RESTful API（因为太流行了），同时SOAP真的成为过去式了。同时现在也有不少其他标准如：HATEOAS, JSON API,HAL,GraphQL 等

```

#### 下一代的API Server

- GraphQL & Relay

- Falcor


```note
GraphQL 赋予客户端强大的能力（也是职责），允许它来实施任意的查询接口。结合Relay，它能为你处理客户端状态和缓存。在服务器端实施GraphQL看起来比较困难而且现有的文档大部分是针对Node.js的

网飞(NetFlix）的Falcor 看起来它也能提供那些Relay和GraphQL提供的功能，但是对于服务器端的实现要求很低。但现在它仅仅是开发者预览版没有正式发布。
```

#### 关于聚合

- batch fetch
- ql.io


```note
所有这些有名的标准规范有他们各种的奇怪之处。一些是过于复杂，一些只能处理读取没有覆盖到更新接口。一些又严重和REST走失。许多人选择构建它们自己的，但是最终也要解决它们设计上带来的问题。

我不认为现在有什么方案是个大满贯（完美的），但是下面是我对API应该有的功能的一些思考：

It should be predictable. Your endpoints should follow consistent conventions.
It should allow fetching multiple entities in one round trip: needing 15 queries to fetch everything you need on page load will give poor performance.
Make sure you have a good update story: many specifications only covers reads, and you’ll need to update stuff sometimes.
It should be easy to debug: looking at the Chrome inspector’s network tab should easily let me see what happened.
It should be easy to consume: I should be able to easily consume it with fetch, or have a well supported client library (like Relay)
我没有找到能覆盖这些需求的方案。如果有，务必让我知道。
同事考虑，如果你实施一个标准化的RESTful资源路径时，使用Swagger来文档化我们的API

```

### 微服务和Node.js

#### 什么是微服务


需要在一些节点加上agent，实现动态扩容等

#### 微服务集成开发

- docker 部署
- 无状态（随时被动态扩缩容掉
- request监控（链路追踪
- 配置加载管理（环境变量

```note
那么我们想实践微服务，node.js web上需要做哪些工作了？

```

### Koa2 与 Node.js

#### What

- 何时发布
- 哪些改动

* koa2中 的 es7 什么时候 release
	大家应该会特别关心，koa2 发布了吗，你们敢用一个未发布的xxx，事实上（issues中说的那样xxx）

* 为什么 koa2

#### Why

#### 相关中间件

* koa2那些中间件和扩展（罗列出来围绕 koa2）
	- 常见的middleware等

	- compose koa
	- koa-adapter （常见中间件的引入）
	- 统一的错误处理
	- 文件即路由
	- koa-validator


### node 一般

#### ES6化

babel 编译和上线
eslint

#### npm scripts 构建过程

https://github.com/gf-rd/blog/issues/20
* npm scripts 使用 - 去掉多余的gulp
* npm 的一些hook点

- 及时更新依赖
- 文件改动重运行 file watch
- debug
- 代码质量检查，入库检查 
- 测试覆盖率

#### JS Smells 代码坏味道

#### 实战上线（Production Deploy & Security）

#### 其他（[Node] - 16年，新 Node 项目注意点）
  
  https://github.com/gf-rd/blog/issues/29
  
  - 异步函数支持回调惯例和Promise新写法
  - 智能的 .npmrc 和正确的版本管理做法
  - npm init
 
 
### 其他web开发遇到的问题

看时间允许，我们再看看node上下游的一些组件的使用

- （画一个完成的链路图，把这些组件串联起来）
- 关于openresty & haproxy & varnish
* 关于 mongodb
- 关于log，监控和指标计算
- 关于扩容，k8s
- 关于 kakfa 等


### 结束
一起来玩：
撩开袖子开始动手吧，到我们这里玩node.js吧

reference，罗列ppt中有的那些


#### 扩展阅读：


##### API Server

https://strongloop.com/strongblog/nodejs-loopback-deployd-api-serve/
Todo: 更新下新时代的 relay falcor 数据接口


the first three generations of API apps. 

Birth of API Server:
like Java's Sprint MVC or Struts. Rails, Django. sails, geddy - include a database abstraction (typically an ORM for RDBMS only), templating libraries for rendering HTML, and a base controller for gluing the two together.  但是 ajax 变多，客户端自己灵活的渲染模板， mvc based backend stretched to serve as both the web application and API server. controllers would implement some methods that returned an entire rendered page while some methods that would return data


the Thin Server:
with app app moving into client and an entirely separate api server. both could be thinned out. web app no longer could directly access the database - this access instead could be pased through to the client. frameworks and databases: MongoDB, CouchDB, Sinatra, Express, Deployd, Meteor and others.

making data on the wire the first class citizen. surfacing database style APIs to JSON document databases. by need data on the wire to transfer state to and from various data sources(database, backend services, etc)
client require much more from an API server. access to raw data is important, but clients require much more from an API server: data aggregation, security, validation, and denormalization are usually a requirement.


Optimized API Delivery:
一个pass-through的api 不利于大型项目的扩展，因为需要access to many data sources. 打包请求的特性让 building api simple is helpful. 但是把这些特定当做黑盒子让customization 实施起来很头疼。最终 REST 和 http 变成了api服务器的标准，但是should not dictate the design of our API server


打开黑盒子！！
Next generation API servers should provide all the out of the box features clients require, especially the hard parts, and provide hooks everywhere. 如 app.remoutes() API 返回 apis http routes, schema definitions, 和其他跟你api服务器相关的元数据信息用于 scaffolding client app, generating 文档，


Data Accesses:
大型项目使用很多数据源： databases, other REST APIs, proprietary services等  API server should provide an abstract API to these data sources that allow you to describe relationships and do ad hoc aggregation and filtering.
to define a list of access controls. 通过配置很容『now only the user that owns an account may read or write it』


Aggregation and Mashup:
聚合通过简单的 joining of data. (denormalize data from various sources, drop data into JSON document database)
譬如用户首页包括的数据可能 span many datasources. aggregated into a single document and easily fetched when homepage is loaded. 问题是 latent ahead-of-time denormalization. 可能会过期（ Java的 Teiid 来解决， without latent copying or moving data）- Loopback 中是 allows you to define relationships between data, even from distributed data sources, and request the aggregate data based on relationship.
如the product and product detail data could come from an inventory database, and the related products could come from a separate backend REST or even SOAP API.

curl http://api.myapp.com/api/products/42
 ? filter[include] = productDetails
 & filter[include]  = relatedProducts


REST:
treat REST as transport, 而不是编程模型。API server 应该跟容易实施 REST api. 不要和 http 混合了你的数据应该也可以通过 web sockets 去访问(just provide a client access to another protocol)


浏览器和移动端应用， 复杂度在增加， 需要更高级的 data acdess. several generations of API servers and frameworks has risen to meet this demand by supporting the requirement of these rich applications.
;

