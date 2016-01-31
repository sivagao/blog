---
title: 【Node】Node 接入层 - 微服务架构
categories: 技术
tags: [Node.js, JavaScript]
date: 2015-11-30 07:56:29
keywords: Nodejs, 前端, ES6
---


前言，文章作者是Chris Richardson，微服务领域的大牛。经常在[microservices.io](http://microservices.io)上发表有关微服务的文章。

## 微服务简介

### 单体架构
尽管也是模块化逻辑，但是最终它还是会打包并部署为单体式应用。
单体式应用也易于部署，只需要把打包应用拷贝到服务器端，通过在负载均衡器后端运行多个拷贝就可以轻松实现应用扩展。在早期这类应用运行的很好。

### 单体架构的不足
一个简单的应用会随着时间推移逐渐变大。在每次的sprint中，开发团队都会面对新“故事”，然后开发许多新代码。几年后，这个小而简单的应用会变成了一个巨大的怪物。
从而敏捷开发和部署都举步维艰，应用变得太复杂，以至于单个开发者不可能搞懂他。维护下变得很差，修改bug和添加新功能变得很困难.应用越大，启动时间会越长。
应用越大，启动时间会越长。一个模块是CPU密集的， 一个模块是内存占有大的，模块部署在一起的单体，不能不在硬件选择上做一个妥协。
可靠性很低，所有模块运行在一个进程中，任何一个模块中的一个bug，比如内存泄露，将会有可能弄垮整个进程。
单体式应用使得采用新架构和语言非常困难。比如，设想你有两百万行采用XYZ框架写的代码。如果想改成ABC框架，无论是时间还是成本都是非常昂贵的

### 微服务架构
很多公司如amazon，ebay，netflix 等通过微服务架构解决以上问题，将应用分解为小的，相互连接的微服务。

一个微服务一般完成某个特定的功能，比如下单管理、客户管理等等。一些微服务还会发布API给其它微服务和应用客户端使用。其它微服务完成一个Web UI，运行时，每一个实例可能是一个云VM或者是Docker容器。

每一个后台服务开放一个REST API，许多服务本身也采用了其它服务提供的API。应用客户端（iOS，Android，web）不直接访问后台服务，而是通过 API Gateway 来传递消息。 其负责负载均衡，缓存，访问控制， API 计费和监控等任务。

这种微服务架构模式深刻影响了应用和数据库之间的关系，不像传统多个服务共享一个数据库，微服务架构每个服务都有自己的数据库。

## 微服务模式

![image](https://cloud.githubusercontent.com/assets/697853/11462376/5d3d46ac-974e-11e5-9cc5-cbc6349e2fc1.png)


核心模式
- 单体架构
- 微服务架构
- API 网关

部署模式
- multiple service instances per host
- service instance per host
- service instance per vm
- service instance per container

服务发现
- 客户端发现
- 服务器端发现
- 服务注册
- 自行注册
- 第三方注册

数据层
- database per service

Tip：

- The Monolithic architecture is an alternative to the microservices architecture.
- The API Gateway pattern defines how clients access the services in a microservices architecture.
- The Client-side Discovery and Server-side Discovery patterns are used to route requests for a client to an available service instance in a microservices architecture.
- The Messaging and Remote Procedure Invocation patterns are two different ways that services can communicate.
- The Single Service per Host and Multiple Services per Host patterns are two different deployment strategies.
- The Database per Service pattern describes how each service has its own database.


PS:
其他的pattern，如 in the area of resiliency and stability. - circuit breaker pattern, stand-in services, or bulkheads.

## 服务发现

### service side 服务发现

![image](https://cloud.githubusercontent.com/assets/697853/11462378/6c14da82-974e-11e5-92fe-4fb94bd86b83.png)


上下文：
服务通常需要调用其他服务.  在单体应用中， 服务的相互调用是通过程序语言层的方法或者是IPC。
在传统的分布式系统中， services run at fixed, well known locations(hosts and ports) and so can easily call one another using HTTP/REST or some RPC 机制。
在新的微服务架构中， application 通常泡在虚拟化或容器环境中（而服务的实例数量和位置可能会动态改变），所以需要一种机制来解决服务的调用方 to make requests to a dynamically changging set of ephemeral service instances(如根据负载情况，EC2 Autoscaling group adjust) 问题就是调用方（如 api gateway）如何discover the location of a service instance?

关注点：

解决方案：
When making a request to a service, the client makes a request via a router (a.k.a load balancer) that runs at a well known location. The router queries a service registry, which might be built into the router, and forwards the request to an available service instance.

举例:
ELB
Some clustering solutions such as Kubernetes and Marathon run a proxy on each host that functions as a server-side discovery router. In order to access a service, a client connects to the local proxy using the port assigned to that service. The proxy then forwards the request to a service instance running somewhere in the cluster

好坏对比:
好处：
对比 client-side discovery，client code is simpler since it does not have to deal with discovery. instead, a client simply makes a request to the router

坏处：
router是个单独的系统需要安装和配置，也需要 replicated for availability and capacity
More network hops are required than when using Client side discovery


## 数据层

微服务下的数据库架构
keep each microservice’s persistent data private to that service and accessible only via its API.

- Private-tables-per-service – each service owns a set of tables that must only be accessed by that service
- Schema-per-service – each service has a database schema that’s private to that service
- Database-server-per-service – each service has it’s own database server.

to create barriers that enforce this modularity.

好坏对比:
好处：
服务是低耦合的。 changes to one service’s databases does not impact any other services
每个服务使用他们合适类型的数据库. text search - elasticsearch, 操作社会化图的用Neo4j

坏处：
implement business transaction that span multiple services is not straightforward(分布式的事务要避免因为CAP. nosql通常不支持事务. 使用最终一致性的, event-driven architecture - )
queries that join data that is now in multiple database 很麻烦：
- application-side joins.
- CQRS command query responsibility segregation.
管理多个SQL和NoSQL数据库的复杂度

