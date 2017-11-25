---
draft: true
---


![](media/14903174964297.jpg)


除了流量分发。Load balancers are effective at:

	•	Preventing requests from going to unhealthy servers
	•	Preventing overloading resources
	•	Helping eliminate single points of failure

同时，部分还包括：

	•	SSL termination - Decrypt incoming requests and encrypt server responses so backend servers do not have to perform these potentially expensive operations
	•	Session persistence - Issue cookies and route a specific client's requests to same instance if the web apps do not keep track of sessions

同时为了 Load Balancer 自己的单点故障，可以启动多台。模式可以是  either in active-passive or active-active mode.
应对 HAProxy 的单点问题 （To eliminate this SPOF, you can use keepalived - all you need is an extra virtual IP address http://www.keepalived.org/）


Layer 4 load balancing 和 Layer 7 load balancing




## Load Balancer 和 Reverse Proxy 区别

	•	Deploying a load balancer is useful when you have multiple servers. Often, load balancers route traffic to a set of servers serving the same function.
	•	Reverse proxies can be useful even with just one web server or application server, opening up the benefits described in the previous section.
	•	Solutions such as NGINX and HAProxy can support both layer 7 reverse proxying and load balancing.


## Nginx 和 HaProxy 异同

## 负载均衡算法

1、roundrobin
表示简单的轮询，每个服务器根据权重轮流使用，在服务器的处理时间平均分配的情况下这是最流畅和公平的算法。该算法是动态的，对于实例启动慢的服务器权重会在运行中调整。
2、leastconn
连接数最少的服务器优先接收连接。leastconn建议用于长会话服务，例如LDAP、SQL、TSE等，而不适合短会话协议。如HTTP.该算法是动态的，对于实例启动慢的服务器权重会在运行中调整。
4、source
对请求源IP地址进行哈希，用可用服务器的权重总数除以哈希值，根据结果进行分配。只要服务器正常，同一个客户端IP地址总是访问同一个服务器。如果哈希的结果随可用服务器数量而变化，那么客户端会定向到不同的服务器； 该算法一般用于不能插入cookie的Tcp模式。它还可以用于广域网上为拒绝使用会话cookie的客户端提供最有效的粘连；




https://forums.rancher.com/t/ranchers-internal-haproxy-for-websocket-balancing/2485
HAProxy has had built in support for websockets since 1.5 something.
You may want to add a tunnel timeout by adding timeout tunnel 3600s to the custom haproxy.cfg defaults section.


balance 的策略使用 source (means that connections will be assigned to a webserver based on IP address.

Websockets use TCP as a transport layer, so we need to configure ELB to transfer all TCP traffic from port 80 to our HAProxy port. The same goes for secure websockets (wss) – all traffic from SSL on port 443 needs to be forwarded to 8005 – a port which our HAProxy container is running on.

from socket.io performing its handshake in the beginning. This means that the second request must end up with the same server as the first one

backend websockets
  balance source
  option http-server-close
  option forceclose
  server ws-server1 [Address]:[Port] weight 1 maxconn 1024 check


F5 - HAProxy

https://serversforhackers.com/using-ssl-certificates-with-haproxy
嗯那就都透传到后面，我们应用LB去做解析
能用F5的https解析，然后把请求打到后面的我们的haproxy吗，它去做LB

F5解析能力很弱，最多2000/s，而且有应用已经用了1部分了





http://stackoverflow.com/questions/15907219/ha-proxy-balancing-by-source-doesnt-appear-consistent


The source IP address is hashed and divided by the total weight of the running servers to designate which server will receive the request. This ensures that the same client IP address will always reach the same server as long as no server goes down or up. If the hash result changes due to the number of running servers changing, many clients will be directed to a different server. This algorithm is generally used in TCP mode where no cookie may be inserted. It may also be used on the Internet to provide a best-effort stickiness to clients which refuse session cookies. This algorithm is static by default, which means that changing a server's weight on the fly will have no effect, but this can be changed using "hash-type"

http://cbonte.github.io/haproxy-dconv/1.6/configuration.html


hash-type <method> <function> <modifier>

The default hash type is "map-based" and is recommended for most usages. The
default function is "sdbm", the selection of a function should be based on
the range of the values being hashed.


uri-->基于uri生成hash表的算法,主要用于后端是缓存服务器
hdr(name)-->header基于首部的信息来构建hash表
source-->基于hash表的算法,类似于nginx中的iphash

http://blog.haproxy.com/2013/04/22/client-ip-persistence-or-source-ip-hash-load-balancing/

非常好的文章
stickiness: it means all the requests from a client must be sent to the same server.


source ip stickness:
we can “rely” on to perform stickiness is the client (or source) IP address.
Note this is not optimal because:
  * many clients can be “hidden” behind a single IP address (Firewall, proxy, etc…)
  * a client can change its IP address during the session
  * a client can use multiple IP addresses

two ways of performing source IP affinity:
  1. Using a dedicated load-balancing algorithm: a hash on the source IP
  2. Using a stick table in memory (and a roundrobin load-balancing algorithm)


hash 算法：
The following events can make the hash change and so may redirect traffic differently over the time:
  * a server in the pool goes down
  * a server in the pool goes up
  * a server weight change

The main issue with source IP hash loadbalancing algorithm, is that each change can redirect EVERYBODY to a different server!!! That’s why, some good load-balancers have implemented a consistent hashing method which ensure that if a server fails, for example, only the client connected to this server are redirected. The counterpart of consistent hashing is that it doesn’t provide a perfect hash, and so, in a farm of 4 servers, some may receive more clients than others. Note that when a failed server comes back, its “sticked” users (determined by the hash) will be redirected to it.
使用 stick-table
to store the source IP address and the affected server from the pool. 使用 non-deterministic load-balance 算法（如 leastconn, roundrobin）Once a client is sticked to a server, he’s sticked until the entry in the table expires OR the server fails.main advantage of using a stick table is that when a failed server comes back, no existing sessions will be redirected to it. Only new incoming IPs can reach it. So no impact on users.possible to synchronize tables in memory between multiple HAProxy
stick-table type ip size 1m expire 1h
stick 等指令




http://blog.haproxy.com/2012/03/29/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/
LB 算法 - to pick up server from a web farm to forward your client requests to

放有多个应用实例可访问，The user will get back to the login page since the application server can’t access his session: he is considered as a new user.


4 种方式应对：
 • Use a clustered web application server where the session are available for all the servers
 • Sharing user’s session information in a database or a file system on application servers
 • Use IP level information to maintain affinity between a user and a server
 • Use application layer information to maintain persistance between a user and a server


第二种是默认的（session等数据是中央存储）
第三种：source IP affinity is the latest method to use when you want to “stick” a user to a server.（）最后之选）-
第四种：use is the Session Cookie, either set by the load-balancer itself or using one set up by the application server.

注意区分 persistence and affinity
se an information from a layer below the application layer to maintain a client request to a single server
persistence means that the server can be chosen based on application layer information.

注意不同的 balance 算法：
Some algorithm are deterministic, which means they can use a client side information to choose the server and always send the owner of this information to the same server. This is where you usually do Affinity  IE: “balance source”
Some algorithm are not deterministic, which means they choose the server based on internal information, whatever the client sent. This is where you don’t do any affinity nor persistence  IE: “balance roundrobin” or “balance leastconn”


对于已经有 session info 的request（ • the request DOES NOT PASS THROUGH the load-balancing algorithm）

 • Let the load-balancer set up a cookie for the session.
 • Using application cookies, such as ASP.NET_SessionId, JSESSIONID, PHPSESSIONID, or any other chosen name

对于第二种应用首先设置sessionId：Set-Cookie: JSESSIONID=i12KJF23JKJ1EKJ21213KJ
haproxy 会对它做修改： Set-Cookie: JSESSIONID=s1~i12KJF23JKJ1EKJ21213KJ （s1用于判断分发到哪台服务器上，进入进出修改cookie值）


  balance roundrobin
  cookie SERVERID insert indirect nocache
  server s1 192.168.10.11:80 check cookie s1



backend bk_web
  balance roundrobin
  cookie JSESSIONID prefix nocache
  server s1 192.168.10.11:80 check cookie s1


（You can use cookie based persistence but socket.io doesn't send a JSESSIONID or the like back to the proxy server）
