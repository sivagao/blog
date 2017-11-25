---
title: Go Basic
---

## Golang 语言特点

“Go is not meant to innovate programming theory. It’s meant to innovate programming practice.”

1 出身名门，血统纯正
2 开发效率，运行效率
3 学习曲线（类C语法，内置GC，编译快速，调试方便
4 天生并发（多核支持，轻量并发，简易同步
5 Unix精髓（组合无对象，无侵入接口
6 标准库（网络编程必备库，系统编程必备库
7 部署方便（交叉编译，无依赖部署
8 稳定（编译检查，编码规范，工程工具

总结几点如下：
Go 成为云基础设施的语言。
单体属性：可以为各个现代的平台生成体积小的，静态链接的，本地可编译的执行文件。
编写多线程（即并发性）网络服务器语言的首选，它让编写多线程网络服务器不再是一种剧痛（goroutine）
新颖的类型系统使得程序结构灵活而模块化

## 微服务下的 Golang

在微服务中相关component的golang实现参考。我们新的微服务相关只需要选择相应的组件就能快速搭建出成熟稳定高效的框架，比起现在 node.js 社区提供的还是丰富不少。Spring Cloud 也为 Java Spring 应用开发 Cloud-Native 提供不少支持。

负载均衡： seesaw, caddy
服务网关：tyk，fabio，vulcand
进程间通信：RESTful（beego，gin，go-kit，goa，micro，lris），RPC（grpc，thrift，hprose）
服务注册发现：etcd，consul，serf
分布式调度：k8s, swarm
异步消息队列：NSQ，Nats
APM：appdash，Cloudinsight，opentracing
分布式配置管理：etcd, consul, mgmt
日志分析：Beats，Heka
服务监控/告警：open-falcon, prometheus
CI/CD：Drone
熔断器：gateway，Hystrix-go

## Golang 编写微服务

开发一个服务很简单。譬如用户服务，它通过http进行serve（使用简单的net/http.Serve来编写.
但是当你的系统由很多这样的服务组成，它们可能会：不同的管理错误的方法，不同后天日志记录格式，处理malicious请求的理念。同时，这样的架构（微服务）需要不少管道plumbing支持（沟通各个组件和服务之间）。如连接池、安全性校核、从错误中优雅的恢复、管理度量和instrumentation、路由日记等等

所以我们使用 go-kit 来提供这些 microservice plumbing 的工作（对比下来 node社区这些还是比较少，没法容易的cloud-native的应用）


实战：

go get github.com/go-kit/kit/examples/stringsvc3


运行 三个普通实例，一个proxy实例，然后是测试的client

$GOPATH/bin/stringsvc3 -listen=:8001
$GOPATH/bin/stringsvc3 -listen=:8002
$GOPATH/bin/stringsvc3 -listen=:8080 -proxy=localhost:8001,localhost:8002,localhost:8003

测试：
for s in foo bar baz ; do curl -d"{\"s\":\"$s\"}" localhost:8080/uppercase ; done

使用 endpoint 中间件来 transport-domain concerns. 譬如 circuit breaking 和 rate limiting.
使用 service 中间件来 business-domain concerns - 譬如 logging and instrumentation

PS:
中间件 - take endpoint and returns endpoint
pass a logger directly into our stringService implementation, but there’s a better way. Let’s use a middleware, also known as a decorator.

代码示例：

服务中间件 - loggingMiddleware

```go
type loggingMiddleware struct {
 logger log.Logger
 next   StringService
}
func (mw loggingMiddleware) Count(s string) (n int) {
 defer func(begin time.Time) {
  mw.logger.Log(
   "method", "count",
   "input", s,
   "n", n,
   "took", time.Since(begin),
  )
 }(time.Now())

 n = mw.next.Count(s)
 return
}

svc = loggingMiddleware{logger, svc}
```

safety middlewares 安全中间件

演示使用 ratelimitter, load balance(使用retry配置)，circuit breaker 等对调用安全进行保障

```go
// 在实际开发中使用类似于服务发现的机制如Consul（使用sd package）等，而不是下面配置的instanceList
var (
  instanceList = split(instances)
  subscriber   sd.FixedSubscriber
)
logger.Log("proxy_to", fmt.Sprint(instanceList))
for _, instance := range instanceList {
  var e endpoint.Endpoint
  e = makeUppercaseProxy(ctx, instance)
  e = circuitbreaker.Gobreaker(gobreaker.NewCircuitBreaker(gobreaker.Settings{}))(e)
  e = kitratelimit.NewTokenBucketLimiter(jujuratelimit.NewBucketWithRate(float64(qps), int64(qps)))(e)
  subscriber = append(subscriber, e)
}

// Now, build a single, retrying, load-balancing endpoint out of all of
// those individual endpoints.
balancer := lb.NewRoundRobin(subscriber)
retry := lb.Retry(maxAttempts, maxTime, balancer)
```

其他术语：

subscriber：adapters（它使用提供的工厂函数将每个发现的实例字符串转换为可用的endpoint）
