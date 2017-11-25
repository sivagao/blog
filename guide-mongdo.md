---
title: 【Tech-Guide】Mongodb 简要面试问题
categories:  Tech-Guide
keywords: Mongodb, fullstack, Node.js
date: 2016-02-28 07:56:29
---

从去年开始，团队就开始要从前端要向一个全职能的部门去转变，所以要陆陆续续总结一些后端技术的面试指南，用于团队面试。

## 基本概念
 - 有哪些类型的NoSQL的数据库，各自的应用场景，你是怎么理解的NoSQL的
 - MongoDB 和 MySQL，CouchDB的对比
 - 如怎么处理外键（embedded）
 - 如schema-less(或polymorphic schemas)的使用场景
 - BSON 和 ObjectID 是什么，ObjectID的构成（12字节的组成）
 - namespaces的概念和组成

## 索引相关
 - 什么是covered query
 - mongodb会默认为每个collection创建的索引吗，是什么
 - 对于array类型的field的索引是怎么做的
 - RAM 放不下索引，mongodb会怎么处理
 - 如何看一个query的执行数据，和explan方法的三种模式（queryPlanner, executionStats, allPlansExection)

## Replication & Sharding
 - 解释下mongodb的replication，primary和secondary节点的工作模式和读写
 - sharding 在mongodb下的实施步骤和原理

## Transaction 和 Concurrence
 - mongodb是如何实现并发的（读写锁，多个读，单个写）
 - mongodb 怎么模拟事务（使用embedded document, 支持单文档的原子操作
 - mongodb 支持多文档的ACID 事务吗？

## Internal
 - 关于aggregation的原理，和mongodb提供的三种方法（aggregation pipeline, map-reduce 函数和单目的的aggregation方法和命令）
 - profiler的原理。操作，游标，数据库命令的数据被写到哪个系统的collection中（system.profile collection, a capped collection)
 - journaling的实现，文件存放的地址？（dbPath 如/data/db下的journal子目录）
 - storage engine（不同的存储引擎），mongodb提供了哪些，分别适合于什么场景（特定的engine适合read-heavy workloads, 另外的可以提供更高的吐出量应对写操作 MMAPv1, wiredTiger）

## 其他
 - 为什么业界场所mongodb数据量一大，性能就渣
 - 默认mongodb会多长时间把数据落地到磁盘（60s)，通过什么配置去改(commitIntervalMs, syncPeriodSecs)
 - GridFS 是什么和使用场景（超过BSON单文档16MB大小，用分chunks存放文件到多个文档中）
 - mongodb如何管理连接的?有必要实现连接池吗??（默认是10个）

## 数据建模
 - 设计一个CMS系统
 - 设计一个类似于Google+的SNS系统
  - 支持如下操作的索引和数据schema
  - 查看个人消息流和他人的wall posts
  - 对帖子进行评论
  - 创建新帖子
  - 维护社交图片

## 参考
[官方FAQ](https://docs.mongodb.org/manual/faq/developers/)
[MongoDB APplied Design Patterns](http://shop.oreilly.com/product/0636920027041.do)
[七周七数据库](http://douban.com/?search)


