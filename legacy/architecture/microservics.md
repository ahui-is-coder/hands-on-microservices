# 微服务技术栈概览

下面来看一下微服务相关的技术选型

* 服务开发框架：在微服务开发方面，我们选用Java作为开发语言。市面上的语言众多，特别近几年来，Go、Rust语言作为服务端语言快速崛起。既然如此，我们为什么还要选用基于Java语言呢？尽管Go等语言崛起的很快，但相对依然小众，能够熟练运用这些语言进行生产开发的人才更是少之又少，从人才供给量上就无法满足业务需求。此外，新兴语言的社区相对活跃，版本变化较为频繁，经常出现各种不兼容升级，各种坑，这些都不利于业务的开发。反观Java，虽然大家都批评多年来一直是”八股文“，但一直保持向下兼容，且依然稳重求进。最终要的是，Java拥有”海量“的开发人员基础，人才供给不会成为瓶颈。因此，我们选用Java作为开发语言，并使用Spring Boot作为开发框架。Spring Boot作为Spring框架的一次"革命"，可以说是为了微服务而生的，在本书的后续章节，大家会逐渐体会到选用Spring Boot的原因。
* 服务注册与发现：为了简化实现难度，我们将借助Kubernetes内置的服务和虚拟端口功能，来实现服务的注册与发现。换句话说，我们将服务注册与发现的能力，下推一层到运维平台层。对于微服务这一层，服务的注册与发现是完全透明的。
* 熔断与限流：微服务之间的调用链更加复杂，为了降低＆隔离服务故障造成的影响，一般选用和熔断或限流策略。我们选用Hystrix作为熔断功能的基础库并进行了封装，Guava的RateLimit作为限流功能的基础库并进行了封装。
* 配置中心：这里主要关注配置的自动下发、自动更新和易维护性。我们采用git + cfg4j的模式实现配置中心。
* 后端组件
 * RPC:目前主流的开源RPC框架是gRPC和Thrift。gRPC在设计理念上更为先进，且原生支持HTTP2，可以直接集成到客户端上。Thrift作为老牌RPC框架，历经多家公司的考验，已经非常成熟和稳定，且性能比gRPC更为出色。因此，我们选用Thrift作为本书的RPC框架。
 * 关系型数据库：我们选用市场占有率最高的开源数据库MySQL。Spring Boot内置了多种数据库接入方式，我们会采用JPA和“裸写”DAO两种接入方法，以满足不同应用场景。
 * 内存数据库：”让系统更快“是我们不断追求的目标。当基于磁盘的MySQL数据库不能满足性能需求时，我们选用Redis内存数据库。Redis是一款高性能的内存数据库，在官方的性能评测中，其QPS可达到十万级别[^1]。在接入组件方面，我们选用Redisson。与老牌的jedis等开源库相比相比，Redisson的优势在于将Redis操作与数据结构进行了有机的结合，可以用类似Java内置数据类型的操作方式轻松地使用Redis。
 * 缓存：构建高性能的分布式系统，缓存是必不可少的。Memcached是经典的高性能分布式内存缓存系统，我们选用它作为后端缓存组件。只有后端组件是不够的，还需要与Spring Boot集成。常见的Java客户端有Spymemcached和xMemcached。由于xMemcached采用了NIO模型，我们选用它作为接入库。
 * 消息队列：当系统的同步阻塞处理频繁出现性能瓶颈，甚至拖垮整个系统时，我们可以引入消息队列，将同步处理转为异步处理。消息队列就是为这种场景而设计的。目前比较主流的开源消息队列有Kafka、RabbitMQ等。从设计理念来看，Kafka是一个成熟的分布式流处理平台，更专注于海量消息和分布式拓展性。RabbitMQ则更加专注于消息队列，且兼容AMQP协议。结合我们的需求，选用RabbitMQ更为合适。虽然Spring内置了AMQP的集成方案，但使用起来略为繁琐。我们会以官方客户端为基础，自行构建一套工具类库。
* 日志：我们选用Spring Boot默认的logback作为日志记录系统；使用"EBLK"组合作为日志收集、分析系统。
* 监控：
 * 微服务监控：在微服务部署之后，我们需要对微服务和其所在的容器进行的健康状况进行监控。包括容器的内存、CPU、网络状况，以及微服务的GC等信息。我们选用Prometheus作为数据的收集和查询系统，Grafana作为监控可视化平台。我们会探讨如何向Prometheus发送自定义的监控数据。
 * 业务异常报警：微服务监控可以帮助我们了解服务的健康情况，定位性能瓶颈。但对于系统的业务异常却无能为力。Sentry是一款错误追踪系统，可以帮助我们发现并定位逻辑异常。类似的，我们也会探讨如何集成Spring Boot与Sentry。

[^1]: [How fast is Redis?] (https://redis.io/topics/benchmarks)