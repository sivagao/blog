---
title: 【Node】Node 接入层 - 技术选型
categories: 技术
tags: [Node.js, JavaScript]
date: 2015-12-07 07:56:29
keywords: Nodejs, 前端, ES6
---


## API 网关

具体 API 网关功能介绍请见[Node.js API gateway](https://github.com/gf-rd/blog/issues/3)
具体 API 网关和微服务架构关系见[Node 接入层 - 微服务架构](https://github.com/gf-rd/blog/issues/10)
该 api gateway 基于 loopback， 结合kraken.js框架。

### 特性
- 开发时支持文件改动重启(remy/nodemon)
- 防止node event-loop卡出(lloyd/node-toobusy)
- 客户端支持，生成helper(如Angular resources service)
- 支持node cluster（pm2）
- 支持webapp的profiling（CPU，memory等）（pm2 metrics 导入statsD）
- 支持environment-specific configfile (indexzero/nconf，loopback内置)
- 工业强度的安全头（helmetjs/helmet，krakenjs/lusca）
- api 文档和playground 支持（swagger-api/swagger-ui）
- routes & controllers 的代码结构约束（krakenjs/express-enrouten） 
- 多个data sources & connector 支持（pg, mysql, mongodb等）
- http req query/body 检查和sanitizer（ctavan/express-validator）
- 接口支持精细化ACL控制（loopback内置）
- configured middleware setup（loopback内置）
- 请求频率控制Rate limit（visionmedia/node-ratelimiter，移入nginx中）
- 请求信息log（morgan，移入nginx中）
- 认证授权系统（oauth, cookie, jwt支持)


### 外围系统
外围系统通过 docker 镜像和容器提供服务. 具体实例和把玩可见下方的 aws 地址

redis - data & session cache
mongo - self-hosetd nosql db

pm2(strongloop-pm) - process manager
varnish - http cache server
nginx - reversed proxy and load balance(need haproxy?)

metrics 平台 - statsD + graphite(carbon + whisper + grafana)
- StatsD：负责收集并聚合测量值
- carbon：Twisted(Python)daemon，接受进入的数据
- whisper：存储时间序列数据的数据库
- grafana：提供丰富现代化的图表编辑和Dashboard（替换的graphite Django）

alerts 平台 (seyren 基于graphite）
logs 平台 - ELK （elasticsearch，logstash，kibana）
- logstash: 负责日志的收集处理和存储
- elasticsearch: 负责日志的检索和分析
- kibana: 负责日志的可视化

## 参考
### 框架选型
（为什么不选 restify/hapi/koa/der，选择express，基于express之上的选择（为什么不sail.js, keystone ）
http://nodeframework.com/#rest-api
http://www.capitalone.io/blog/contrasting-enterprise-nodejs-frameworks/
http://www.3rank.com/node-js-frameworks/
https://github.com/krakenjs/kraken-js
https://github.com/strongloop/loopback

### 外围选型
http://blog.takipi.com/graphite-vs-grafana-build-the-best-monitoring-architecture-for-your-application/
https://github.com/scobal/seyren
https://keymetrics.io/
http://rosskukulinski.github.io/talk-statsd/#/26
http://techblog.holidaycheck.com/profiling-mongodb-with-logstash-and-kibana/
http://elk-docker.readthedocs.org/#installation

### 运维性能
node webapp 的 运维
https://strongloop.com/strongblog/category/node-devops/
node 性能优化
https://strongloop.com/strongblog/category/performance-tip/

https://blog.risingstack.com/node-js-security-checklist/
https://blog.risingstack.com/node-js-production-checklist/
http://blog.risingstack.com/operating-node-in-production/
https://blog.risingstack.com/nodejs-production-environment-for-startups/

### web 开发
https://blog.risingstack.com/swagger-nodejs/
http://blog.risingstack.com/web-authentication-methods-explained/
https://github.com/auth0/node-jsonwebtoken
http://www.nearform.com/nodecrunch/release-the-kracken-how-paypal-is-being-revolutionized-by-node-js-and-lean-ux/


## loopback Overview

### 框图
![qq20151207-0](https://cloud.githubusercontent.com/assets/697853/11617734/b8c758e4-9ccd-11e5-9e35-4b7706070d67.png)
![qq20151207-1](https://cloud.githubusercontent.com/assets/697853/11617735/b8c7a75e-9ccd-11e5-831e-a2840b0b332e.png)


## 其他设计参考

### Nginx - 实现一个API Gateway的技术考量

#### 性能和可扩展性:
一个可选的方案是NGINX Plus。NGINX Plus提供一个成熟的、可扩展的、高性能web服务器和反向代理，它们均容易部署、配置和二次开发。NGINX Plus可以管理授权、权限控制、负载均衡、缓存并提供应用健康检查和监控。

#### 采用反应性编程模型：
为了最小化响应时间，API Gateway应该并发的处理相互独立的请求。但是，有时候请求之间是有依赖的（如为了获得客户的产品愿望清单，需要先获取该用户的资料，然后返回清单上产品的信息）
利用传统回调方式限制 callback 地狱.
类似的反应抽象实现有Scala的Future，Java8的CompletableFuture和JavaScript的Promise。
基于微软.Net平台的有Reactive Extensions(Rx) 和RxJS。

#### 服务调用：
JSM，AMQP
HTTP, Thrift
同步，异步机制（基于消息）实现方式是多种的。

#### 服务发现：
如果采用客户端发现服务，API Gateway必须要去查询服务注册处，也就是微服务实例地址的数据库。

#### 处理部分失败：
Hystrix 对于实现RPC，记录那些超过预设定的极限值的调用。采用 circuit breaker 模式， 使得客户端从无响应服务的无尽等待中停止，对于一个错误率超过预设值的，中断服务，在一段时间内所有请求立即失效（防止雪崩），提供fallback（如读取缓冲或返回默认值等） - 如果你在用JVM，就应该考虑使用Hystrix。如果你采用的非JVM环境，那么应该考虑采用类似功能的库


