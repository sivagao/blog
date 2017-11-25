---
title: 监控 101
draft: true
---

监控这一过程开始于通过采集器(collection agents，它是运行在被监控节点如主机，数据库或网络设备上程序)，agent 采集有意义的系统信息，这些信息被加工量化为数据点，按照一定数据大小量级和时间间隔后定时被agent发送上报到监控服务器。然后被存储、聚合（如存储指标的时间序列数据库 Whisper或InfluxDB，日志存储和索引的ElasticSearch等），最终被绘制到我们的监控面板（dashboard）上（如Kibana，Grafana，Graphite Web等）

## 数据采集

 采集器被分为如下类型：

- 白盒
	- log parsers 解析器。从日志条目中抽取特殊的信息，如从web服务器日志中的状态码，请求的响应时间
	- log scanners 扫描器。计算特定字符在日志中出现的次数。譬如要看看普通错误和严重错误的数量，可以看看在日志文件中匹配的 "ERROR|CRITICAL"这个正则的次数。
	- interface readers

- 黑盒
	- probers 探针。它们跑在被监控系统的外部，发送请求给系统和检查它们的响应。譬如对网站发送 ping 请求或 http 调用来验证网站是否可用
	- sniffers 嗅探器。

数据收集模式不仅仅有上面提到的 passive 被动方式，也有 active 主动的方式，通过类似于statsd client 在被监控的节点（如nginx，应用服务器）上主动发送数据给监控系统。

## 数据覆盖

完整的监控应该采集和计算包括如下三个部分的指标：

- 资源可用 resource availability

	系统中的每个操作需要消耗 CPU 计算时钟，并且需要内存，信息交互要占用贷款，数据也占用磁盘空间，在不同设备交换消耗 I/O 通量

- 软件性能 software performance

- 用户行为/业务 user behavior

### 另一个角度：

我们的应用通常包含了三种类型的『软件』：操作系统层面，中间件层面，运行于前两者之上的业务应用层面。每个层面都会产出自己所有组件的特有信息。要对它们进行较为全面的监控，因为系统的短板木桶效用。

- 操作系统：

	虽然与上面提到的资源可用联系紧密，操作系统监控旨在了解资源如何、以什么比例、以及由谁使用。通常，这个层面的度量报：

	- 可用/已用inode信息，磁盘空间等文件系统
	- user-to-system CPU 时间
	- 虚拟内存管理（包括swapping交换和内存统计）
	- 上下文切换和等待队列状态

- 中间件：

	提供了一套标准的可组合的，针对目的的软件组件，它们组合起来作为解决方案的引擎。分布式计算中的中间件包括软件Web服务器和应用程序服务器框架。

- 应用：

应用指标，经常引入特定于业务垂直领域的抽象概念。譬如在批处理系统可以在包含的任务数量和处理时间的指标，内容管理系统可以根据内容条目（如商品，评论，文章等）的变化（新增，更新等）来统计指标。

可以相互协调整个系统中不同层面的组成部分（如应用服务，Linux基础服务器，数据库等）来探知问题的源头。

![](media/14763418507524.jpg)

后面的指标和时间序列，见下一篇讲解。

## 工具篇 - StatsD & Graphite

简而言之：StatsD 负责收集并聚合测量值。之后，它会将数据传给 Graphite，后者以时间序列为依据存储数据，并绘制图表。

```java
// 统计不同类型的订单的量
statsDClient.increment("newOrder:" + serviceType.getType());

// 记录抢单计数总数
statsDClient.increment("snatchOrder");

// 记录抢单计数，以下划线分割单号
// statsDClient.increment("snatchOrder_" + orderId);
```

StatsD是Node.js驱动的网络守护进程。它来监听从UDP或TCP连接来的例如Counter、Timers等统计数据点，作为本地聚合器来计算和采样，最终把这些应用的指标发送到后端的时间序列型的数据库中，用于后续的可视化展示

Graphite由于三个部分组成：

	1.	carbon - Python Twisted（高性能异步IO网络库）写的守护进程来监听和处理进来的时间序列的数据
	2.	whisper - 用于存储时间序列的简单数据库
	3.	graphite webapp - Python & Django Web框架开发的用于渲染图表和编辑仪表盘的Web应用

![](media/14763496126321.png)

通常我们用Grafana替换graphite原始的webapp，来作为我们的dashboard可视化应用，在后续的metrics章节会继续介绍。

PS：也有利用时间序列数据库InfluxDB+Grafana来为alternative替代方案
