---
draft: true
---

Zabbix 监控一览和艺术

## 数据源头

从业务应用，中间件，虚拟层，网络，系统和硬件层面

## 收集模式：active（更好性能和安全） vs passive

- 譬如agent - request frequency set by sent 120 sec. by default
- agent -> server request: want to check? response: cpu load... agent -> server 推送 所需的数据

## check的时机

- every n seconds(不同时间段不同的间隔 - 如work time, weekend 等)
- at specific time

## trigger. the problem definition

- {host:metric.function(arg)} :operator如>,=等
- {node1:system.cpu.load.last()} > 5

## 操作符，函数

[如min, max, last, count, avg, diff, 等]

## art of problem detection

  - 问题：false positives - flapping
  - 问题：too sensitive

  ### take advantage of history

  - {server:system.cpu.load.min(10m)} > 5 过去10min中cpu load最小都大于5..
  - {server:net.tcp.service[http].max(5m)} = 0
  - {server:net.tcp.service[http].max(#3)} = 0 最近三次都

  ### problem -> recovery

  - 系统overload {server:system.cpu.load.min(5m)}>3 recovery就是min(2m)}<1
  - no free disk space： {server:vfs.fs.size[/.pfree].last()}<10，recovery就[/.pffree].last()是>30
  - ssh server not avaiable： {server:net.tcp.service[ssh].max(#3)}=0,recovery就是min(#10)=1

  ### anomaly detection

  - 和norm对比(system state in the past)
  - {server:tps.avg(1h)} < 2*{server:tps.avg(1h,7d)}
  - tps每秒交易数量，2x less上周同一时间段

  ### problem forecasting & trending prediction;

  通过trigger function: timeleft 和 forecast

## reactions to problem

  - automatic problem resolution
  - sending alerts
  - escalate! 危机升级

    - immediate reaction, delayed reation, notification if automatic failed, repeated notification,
    - 升级给老大电话....

## event correlation

  同时多个event相关的，root cause analysis 等

## all-in-one solution 大而全

  如data collection, problem detection, automatic actions,
              alertings[escalations], 趋势预测, 等
