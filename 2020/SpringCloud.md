# Spring Cloud 

## 服务注册和服务发现

### 对比

1、Sping Cloud Eureka

优点：
* CAP：AP,数据最终一致性
* 简单易用：开箱即用，控制台管理

缺点：
* 内存限制 
* 单一调度更新：客户端简单轮训更新，增加服务器压力
* 集群伸缩性限制：广播复制模型，增加服务器压力


2、Spirng Cloud Zookeeper

优点：
* 成熟协调协系统
* CAP理论:CP

缺点：
* 维护成本
* 伸缩性限制

3、Spring Cloud Consul

优点：
* 通用方案：适用于Service Mesh Java 生态
* CAP:AP模型

缺点：
* 可靠性无法保证
* 非Java 生态

|技术选型|CAP模型|适用规模（建议）|控制台管理|社区活跃|
|:--:|:--:|:--:|:--:|:--:|
|Eureka|AP|<30k|支持|低|
|Zookepper|CP|<20k|不支持|中|
|Consul|AP|<5K|支持|高|

### Spring Cloud 服务发现基本模式

1、启动注册中心

2、增加客户端依赖

spring-cloud-starter-*

3、注册客户端

`@EnablerDiscoverClient`

### Spring Cloud Commons 抽象原理

1.激活注册 @EnableDiscoveryClient

2.服务注册 ServiceRegistry、Registration

3.负载均衡 Ribbon Server、ServerList

4.Actuator HealthIndicatror @Endpoint


## Spring Cloud 分布式配置实现

###　对比

1、Git实现

版本化配置，不推荐使用

2、Spring Cloud Zookeeper

Apache Zookeeper

3、Spring Cloud Consul


4、JDBC实现

Edgware 版本引入，不推荐使用

### Spring Cloud Context 抽象-配置相关

1、Spring Environment

EnvironmentManager

2、Bean动态刷新

`@RefreshScope`

3、Spring 应用上下文

RefreshScope

ContexRefresher

scope - refresh

4、 Spring Boot Actuator

EnvironmentEndpoint

WritableEnvironmentEndpoint

5、Spring Cloud 事件

EnvironmentChangeEvent

RefreshEvent

mvn -Dmaven.test.skip -U clean install

## Spring Cloud 服务熔断现状-Hystrix

1、熔断模式

服务超时（Timeout）

信号量（Semaphore）

2、编程模型

面向接口（Interface）

面向注解（Annotation）

3、策略规则

自定义实现

注解配置

多数据源适配

4、 控制台

Hystrix Dashboard

5、生态整合

JVM进程服务

RPC服务

Spring Cloud Statck 等

## Spring Cloud Commons

1. Spring Cloud Context 应用上下文相关，

spring cloud 应用中创建一个 bootstrap context，她是作为了主应用的 parent context ；主要负责从外部资源（分布式配置）
加载配置以及在解码本地的配置文件。

application context 和 bootstrap context 共享了相同Environment. 默认情况下，bootstrap properties(从配置中心加载的属性) 
有更高的优先级，所以它们不能被本地的配置所覆盖。

bootstrap context使用了和主应用的 context 不同的配置方式，使用的是 bootstrap.yml 而不是 application.yml，
这主要是为了保证两个context的配置可以很好的分离；


2. Spring Cloud Commons

抽象

## Spring Cloud GateWay

底层是netty




