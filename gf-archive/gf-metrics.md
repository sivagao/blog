---
draft: true
---

▾	☐	应用的状态需要上报
	•	☐	health check - docker 下
	•	☐	spring boot 中



## 时间序列的模式

在时间序列上的数据点会根据值变化体现出趋势特征（当然越精细越小时间范围的，数据起伏尖峰越明显。所以要设置统一的时间范围后在观察）

![](media/14763420997963.jpg)




logs will contain crucial, precise information that may never make it into a timeseries—but plotting metrics is much faster, and most of the time you don’t need the precise data logs yield, while you almost always need to see the big picture fast.

日志中包含重要而珍贵的信息，大部分时间你不需要特点的某个日志（除非是debug），而是需要对流出的日志条目在时间序列上抽取，计算和绘制，从而从趋势上快速发现问题所在。


Metrics

监控指标是一系列的被分组的连续、周期、有序的数据点集合。每个数据点由被测量后记录的数值，记录采集时间的时间戳和描述数据点的系列属性（如主机名，ip等）所组成。

我们指标中的数据点被按照固定时间间隔被分块，通过某种有意义的数学变换来概括和统计，最终可以被以时间序列的形式被呈现，然后绘制二维图表。

数据点间隔 - time granularity

通常有1，5，15 和 60分钟

时间序列来作为监控的基础是因为它可以非常清楚的展现在历史数据这上下文中的数据变化趋势。从而回答了：发什么了什么变化，何时发生了这些变化的问题。


摘要统计

Summary statistics

- n: 数据点发生的次数
- sum: 所有数据点的总和
- avg: 数据点的平均值
- p0-p100
- deviation: 被收集数据点的标准差




The length of data point intervals, also referred to as time granularity, depends on types of measurements and the kind of information that is to be extracted. Common intervals include 1, 5, 15, and 60 minutes, but it is also possible to render intervals as granular as one second and as coarse one day.

The single most important advantage of using timeseries for monitoring is their property of accurately illustrating the process of change in a context of historical data. They are an indispensable tool for finding the answer to the critical question: what has changed and when?

Timeseries are two-dimensional with data on the y-axis and time on the x-axis. This means that any two independent timeseries will always share one dimension—time. This way, plotting data from multiple metrics against one another adds just a single layer of complexity at a time to the chart. For that reason, timeseries provide an efficient means of highlighting correlative relationships between data from many sources, such as interlayer dependencies in a software stack.


### frequency distribution and percentiles

### rate of change

### time graularity



## 工具 - Grafana

grafana - http://docs.grafana.org/guides/basic_concepts/ Beautiful metric & analytic dashboards
The leading tool for querying and visualizing time series and metrics
