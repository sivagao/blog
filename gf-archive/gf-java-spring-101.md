---
draft: true
---

PPT 一览，下载见附件。

![](media/14764133008212.jpg)


## Highlights

- Java 发展之路

![](media/14764133948101.jpg)


- 新语言，新特性

![](media/14764134093311.jpg)


- Spring 框架一览

![](media/14764134859113.jpg)


## Speak Note

新一代的java会让大家的开发轻松很多，
java很复杂繁琐头疼（大家提到的时候， think in java, effective java大部头的书，但是现在不用纠结传统的语言层面上的东西了。

去年是java 20周年，orcale有很大的庆祝活动，收购sun（对community好事坏事，它是律师组成公司），现在java之父已经离开了（oracle对商业和社区规范化很大贡献。

对java 编程语言本身
1.0
之后的， 基础的JIT compiler，Hotspot的vm，
从 5.0之后支持泛型，让java类型表达更丰富（这个是非常重要的版本
c#.net 中早就有了（尤其），即使j8的lambda，closure也在其他中早就有了
但是选择语言不是只看语言本身，看类库。社区提供了很多强大的工具类库等

非常有趣的是不仅仅贡献java语言本身，也贡献了另外的jvm，和在它之上的（业界领先的vm，高性能的，同时（遵循jvm语言规范后）对语言支持很好）
上面scala「spark选用它作为主要的编程语言」，groovy（很多spring里，很多在用
JavaScript可以在jvm上跑（
clojure



java语言本身不足通过库和生态系统去弥补。大概的趋势
你会看见分为两大阵营（web上，java ee, 和非 java ee）。以为orcale正规军的servlet，jsp，jsf很多东西混在一起没有分离，后面需要model去做binding。虽然它是标准，但是很多人乐于喜欢用spring mvc，很少用structs很多人不用被取代，ssh(spring structs, hibernate)
总体看有java ee的实现，也有非ee的实现（大部分是spring的实现。所以它是java生态系统非常重要的一点。


java和.net还是比较像（静态的，某种意义跨平台，都是比较重
python,ruby 另外的（数据分析，django, flask 做一些web开发，ruby 本身在rails，
php不用多说，世界上最好的编程语言（所以不用，facebook第一版，能开发出好的语言就可以
前端程序员打开新思路（JavaScript居然可以写后端，它的不足有callback hell, quick & dirty但是当你code规模变大，企业开发，但是维护性或者嵌套不容易debug等

java写起来很复杂，verbose，python就很简洁。真的这样吗？


spring 初期，哲学都一样，用xml做configuration，但是Spring经历了这么多，意识到那么多conf有不足*（spring source，pivotal收购各种被收购，对spring做了一些改造，我觉得是比较好的方向，但是介绍之前，我讲些：

大家看下java招聘，跟web相关，都会要求懂spring。我觉得是整个生态系统
不仅仅framework, 
spring core 只是bean的container）为什么，听过DI，这个container来维护bean的生命周期。
除了core之外，包含了web，mvc，
aop（切面编程）比如logging，security等，这些和业务code是要隔离的，譬如对log改动可以都改动等。
支持事务，支持JPA，支持ORM等对象关系模型等，ejb（java ee 来控制transaction，而ejb很复杂，大家很struggle）。


what do you like/dislike spring？
下面是大家可能喜欢或者不喜欢的Spring的一些点。
首先DI是大家最有可能，DI其实是一种Design pattern。大家在OO时如果cls a用cls b（new b前需要b是怎么构成的，每个cls需要它依赖是怎么生成的。所以在center的地方来申明b是怎么生成，所以Spring扫描来生成和放置，别人需要用直接autowire就行了）

mvc，spring提供在每一层都提供了很多好用的来帮助开发者
大家不喜欢的，在spring 是java写的，初期还是繁琐的理念，手动的配置和xml配置的，很多xml要有两千多行来配置。
可以想想这么多行，那么多bean、component，如果加减改动非常麻烦和不可想象。
在学校是通过xml来改动，延续下来。变成 legacy的。

Spring和 application server 有冲突的，为什么（application server一开始是code against j2ee standard，而不是spring standard，中间有
学习曲线很多东西要记住，这样才能处理一个request等


到底是谁的问题，是java本身还是spring社区导致的问题，java ee等

于是深思熟虑后，
所以spring社区就孕育而出的Spring boot。

为什么是？framework本身提供不同的各种素材（但是你要知道它这些要怎么用，才能做出来好的菜）但是spring boot让你做很少工作，让你做出好吃的菜（能够大部分满足需求

在Spring boot发布后，是非常好的，微服务的开发的方式（非常好的数据，java社区、Spring社区意识到了boot解决了很多问题所以乐于使用。为什么要使用Springboot

首先：
CoC，有一些opinionated的帮你auto config很多东西，譬如安全配置，datasource setting，integration各种预设的值，你改改参数，你不用知道那个bean在哪里生成，他们之间什么关系。同时预留接口，不满足后你可以overwrite它（rails更大量用这样的技巧coc）

profiles，本地有开发环境还有staging, production, 等。但是同样code在不同环境需要不同的参数。（spring profiles 来控制 Properties

同时结合（war file 打包，下载单独的Tomact，通过Tomact admin console或directory后才能使用。
但是spring boot 结合到tomcat, jetty不需要单独，完整打包在一起，直接 javac 去run就好了 这个embedded servlet containers 让部署非常方便

actuator 让operation运维的痛点（譬如application health, 还是启动时ping下还是怎么， monitoring来看它的状态，它依赖的状态，同时ops不同系统负责不同方面，但是actuator包含这些方面，所以是production ready


但是光光boot不能解决所有问题，你的app需要访问底层数据库（如sql写过，select from where xx id = xxx等）这种boilerplate希望系统来帮我们生成（save，delete这些逻辑都很简单，需要被节省下来）

于是spring想到这点，有spring data项目包含了很多（如spring-data-redis,抽象层在store和在model之间）
jpa - persistent api 相同的目的，把jpa更加抽象一下（对数据访问更为简单，
spring data让method name和参数来自动生成sql

所以crud（夸得）。这种就非常方便实现xxxx
