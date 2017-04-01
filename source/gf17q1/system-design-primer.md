
# 架构原则的考量

## Performance vs Scalability

## Latency vs Throughout

## Availability vs Consistency

## CAP theorem

## Consistency Patterns

## Availability Patterns



# 传统 Web 架构的考量

## Domain name osystem

## CDN

- push CDNs
- pull CDNs

## Load Balancer

- Availability 
- layer 4 load balancing
- layer 7 load balancing
- horizontal scaling

## Reverse proxy 

## Application layer

## Database

RDBMS

- master-slave replication
- master-master replication
- federation
- sharding
- denormalization
- SQL tunning

NOSQL

- key-value store
- document store
- wide column store
- graph database

SQL or NoSQL 对比


## Cache 缓存

- client caching
- CDN caching
- web server caching
- database caching
- application caching
- when to update the cache （模式和区别）
	- cache-aside
	- write-through
	- write-behind(write-back)
	- refresh-ahead

## Async 异步

- message queues
- task queues
- back pressure

## Communication 通信

- HTTP
- TCP
- UDP
- RPC
- REST


## Security 安全

## Appendix 附录

- Powers of two table
- Latency numbers
- addition system design(各种系统)
- company architecture
- company engineering blogs



