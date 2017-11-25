---
draft: true
---

## 什么是Log

It is an append-only, totally-ordered sequence of records ordered by time. It looks like this:

the log. Sometimes called write-ahead logs or commit logs or transaction logs

## 为什么重要

are at the heart of many distributed data systems and real-time application architectures.

你无法真正理解一个复杂的数据库，nosql 的keyvalue粗糙南湖，复制集，paxos分布式协议，版本控制等。

You can't fully understand databases, NoSQL stores, key value stores, replication, paxos, hadoop, version control, or almost any software system without understanding logs; and yet, most software engineers are not familiar with them.

日志使得应用程序运行的状态变得透明。在原先的基于主机环境中，日志通常被写入到stream到文件中（stdout, stderr）.
它是事件流的汇总，按照时间顺序收集起来，通常是一个事件一行。随着应用在运行时持续的增加。在微服务情况下，应用不应该考虑自己的输出流（
      不去写入，不去存储，不去做logrotate等），应该是把所有的有意义（帮助调试的debug，显示应用状态和事务处理的info，遇到
      异常错误的error-warn）输出到标准输出中。
每个进程的输出流由运行环境截获，并将其他输出流整理在一起，然后一并发送给一个或多个最终的处理程序，用于查看或是长期存档。logsta
      sh, fluent的开源就可以做到。
当然在线上环境或生成测试中，可以通过docker提供的docker log 功能回溯到我们所需要的日志

        • 找出过去一段时间特殊的事件。
        • 图形化一个大规模的趋势，比如每分钟的请求量。
        • 根据用户定义的条件实时触发警报，比如每分钟的报错超过某个警戒线。



Data Integration - making all the data an organization has available in all its services and systems
Logs & Real-time Stream Processing


## log的形式

![](media/14763399939236.jpg)



![](media/14763400225085.jpg)




## log的级别和开启

"dev": "NODE_ENV=dev PORT=9000 DEBUG=Ctrl*,Lib*,Err,Global nodemon --exec node -- $npm_package_main",


动态替换
java -Dlogging.config='/opt/java_clickeggs_oacustomer_9000/log4j2-spring.xml'

![](media/14763398849592.jpg)



![](media/14763398431486.jpg)



## log的管理和使用


几乎所有的应用服务或进程都会创建日志文件。它很可能会慢慢吃掉我们服务器的磁盘空间，所以对日志进行管控非常重要。（我们已经非常多次听到日志搞满磁盘空间后，导致应用down掉的例子了）

服务通常会生产事件或错误信息到日志文件，有些自带管理的，有些没有。譬如管理日志的删除，压缩等
简单的我们可以借助logrotate来实施管理，它可以周期性的读取，压缩，备份，创建新和运行自定义的脚本，同样和删除过期的或缓慢变大的日志文件。

同样，在简单的负载均衡或复杂的分布式系统中，需要把日志采集到统一的地方来中心化分析和处理是必要的。采取rsyslog或更强大的开源工具是必要的。


## 挖掘 Log - 调试，ELK

## Log 的应用场景

日志不仅仅是为了运维需求而生，它同样可以成为实现应用或服务功能的一部分。mongodb的repliset的oplog，和

### MongoDB Replication's oplg

MongoDB 的Replication是通过一个日志来存储写操作的，这个日志就叫做oplog。
that keeps a rolling record of all operations that modify the data stored in your databases. MongoDB applies database operations on the primary and then records the operations on the primary’s oplog. The secondary members then copy and apply these operations in an asynchronous process. All replica set members contain a copy of the oplog, in the local.oplog.rs collection, which allows them to maintain the current state of the database.


### 事件驱动的数据管理

在微服务架构中，每个微服务都有自己私有的数据集。不同微服务可能使用不同的SQL或者NoSQL数据库。尽管数据库架构有很强的优势，但是也面对数据分布式管理的挑战。第一个挑战就是如何在多服务之间维护业务交易一致性；第二个挑战是如何从多服务环境中获取一致性数据。
最佳解决办法是采用事件驱动架构。其中碰到的一个挑战是如何原子性的更新状态和发布事件。 解决方案中除了包括将数据库视为消息队列、交易日志挖掘和事件源。

挖掘数据库交易或者提交日志。应用更新数据库，在数据库交易日志中产生变化，交易日志挖掘进程或者线程读这些交易日志，将日志发布给消息代理。 一个DynamoDB流是由过去24小时对数据库表基于时序的变化（创建，更新和删除操作），应用可以从流中读取这些变化，然后以事件方式发布这些变化。


![](media/14763414232647.png)

### Misc

回放和操作 - kafka 重新消费


## Reference

The Log: What every software engineer should know about real-time data'sunifying abstraction

微服务的事件驱动数据管理

I Love Log
