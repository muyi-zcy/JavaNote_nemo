# Zookeeper   Eureka

## 服务治理中心

服务提供者、服务消费者



## Eureka

分为Eureka server和Eureka client

各个微服务启动时会向Eureka server 注册自己的信息，Eureka server进行存储；

微服务启动后会周期性的（默认30s）向Eureka server发送心跳进行报告自己的状态，如果超过一定时间Eureka server没有收到某个微服务的心跳信息，注销该实例；

Eureka Server同时也是Eureka Client。 多个Eureka Server 实例互相之间通过复制的方式来实现服务注册表中数据的同步；

Eureka Client 会缓存服务注册表中的信息；

### Eureka的自我保护机制

如果在**15分钟内超过85%的节点**都没有正常的心跳，那么Eureka就认为客户端与注册中心出现了**网络故障**，此时会出现以下几种情况：

1. Eureka不再从注册列表中移除因为长时间没收到心跳而应该过期的服务
2. Eureka仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上(即保证当前节点依然可用)
3. 当网络稳定时，当前实例新的注册信息会被同步到其它节点中.

当Eureka Server节点在短时间内丢失过多的客户端时（可能发送了网络故障），那么这个节点将进入自我保护模式，不再注销任何微服务，当网络故障恢复后，该节点会自动退出自我保护模式



**Eureka保证AP**



## Zookeerper

**Zookeeper保证CP**