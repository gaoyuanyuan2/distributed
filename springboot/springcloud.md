# Spring Cloud
### 1、概述
<br>1. 定义
<br>就目前而言，对于微服务业界并没有一个统一的标准的定义(While there is no precise definition of this architectural style)
<br><br>但通常而言，微服务架构是一 种架构模式或者说是一种架构风格，它提倡将单-应用程序划分成一组小的服务， 每个服务运行在其独立的自己的进程中，服务之间互相协调、
互相配合,为用户提供最终价值。服务之间采用轻量级的通信机制互相沟通
(通常是基于HTTP的RESTful API)。每个服务都围绕着具体业务进行构建，
并且能够被独立地部署到生产环境、类生产环境等。另外，应尽量避免统一的、 集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言、工具对其进行构建，
可以有一个非常轻量级的集中式管理来协调这些服务， 可以使用不同的语言来编写服务，也可以使用不同的数据存储。
微服务化的核心就是将传统的一站式应用，根据业务拆分成一个一个的服务，彻底地去耦合每一个微服务提供单个业务功能的服务，一个服务做一件事,
从技术角度看就是一种小而独立的处理过程，类似进程概念，能够自行单独启动或销毁,  拥有自己独立的数据库。
<br><br>简而言之，微服务架构风格是一种将单个应用程序作为一套`小型服务`开发的方法，每种应用程序都运行在自己的`进程`中，并与`轻量级`机制(通常是`HTTP资源API`)
进行通信。这些服务是围绕业务功能构建的，可以通过全自动部署机制`独立部署`，这些服务的集中管理最少，可以用不同的编程语言编写如并使用不同的数据存储技术。
<br><br>2. 优点
<br>每个服务足够内聚，足够小，代码容易理解这样能聚焦一个指定的业务功能或业务需求开发简单、开发效率提高，一个服务可能就是专一的只干一件事。
<br>微服务能够被小团队单独开发，这个小团队是2到5人的开发人员组成。
<br>微服务是松耦合的，是有功能意义的服务，无论是在开发阶段或部署阶段都是独立的。微服务能使用不同的语言开发。
<br>易于和第三方集成，微服务允许容易且灵活的方式集成自动部署，通过持续集成工具，如Jenkins, Hudson, bamboo微服务易于被一个开发人员理解， 修改和维护,这样小团队能够更关注自己的工作成果。无需通过合作才能体现价值。微服务允许你利用融合最新技术。
<br>微服务只是业务逻辑的代码，不会和HTML,CSS或其他界面组件混合。
<br>每个微服务都有自己的存储能力，可以有自己的数据库。也可以有统一数据库。

<br>元素的独立性。没有一个或几个元素，系统可以正常工作。
<br>可扩展性。首先，通过微服务架构，您可以更轻松地扩展团队。其次，更高的可扩展性会影响开发速度。每个子团队都有自己的积压工作并独立交付，因此他们可以将开发速度提高5倍。
<br>灵活性。如果您需要申请或尝试新技术，微服务将允许您更轻松地合并它。
<br>降低进入障碍。您的开发团队正在成长。因此，您决定切换到微服务以改进工作流程。这种好处有两种方式：新专家可以更快地进入微服务架构。

<br><br>3. 缺点
<br> 开发人员要处理分布式系统的复杂性
<br> 多服务运维难度，随着服务的增加，运维的压力也在增大系统部署依赖服务间通信成本数据一致性系统集成测试性能监控.....

<br>分布式系统。多个模块和数据库之间有很多连接。因此，必须非常小心地处理请求，事务，数据管理。
<br>测试。整体架构使用开箱即用的WAR。QA专家只需启动该文件即可确认monolith与底层数据库的连接。相反，微服务要求在开始测试之前验证每个服务。

<br><br>
![对比](https://github.com/gaoyuanyuan2/distributed/blob/master/img/16.png) 
<br><br>
![对比](https://github.com/gaoyuanyuan2/distributed/blob/master/img/17.png) 
<br><br>
<br>4. SpringCloud和SpringBoot是什么关系
<br>SpringCloud,基于SpringBoot提供了一套微服务解决方案，包括服务注册与发现，配置中心，全链路监控，服务网关，负载均衡，熔断器等组件，除了基于NetFlix的开源组件做高度抽象封装之外，还有一些选型中立的开源组件。
<br>SpringCloud利用SpringBoot的开发便利性巧妙地简化了分布式系统基础设施的开发，SpringCloud为开发人员提供 了快速构建分布式系统的一些工具，包括配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等,它们都可以用SpringBoot的开发风格做到-键启动和部署。
<br><br>SpringBoot并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过
<br>SpringBoot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包
<br><br>SpringBoot专注于快速方便的开发单个个体微服务。
SpringCloud是关注全局的微服务协调整理治理框架，它将SpringBoot开发的一个个单体微服务整合并管理起来,
为各个微服务之间提供，配置管理、服务发现、断路器、 路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等集成服务
<br><br>SpringBoot可以离开SpringCloud独立使用开发项目，但是SpringCloud离不开SpringBoot, 属于依赖的关系
<br>SpringBoot专注于快速、方便的开发单个微服务个体，SpringCloud关注全局的服务治理框架。
<br><br>5.最大区别: SpringCloud抛弃 了Dubbo的RPC通信，采用的是基于HTTP的REST方式。
<br><br> 严格来说，这两种方式各有优劣。虽然从一定程度上来说，后者牺牲了服务调用的性能，但也避免了上面提到的原生RPC带来的问题。而且REST相比RPC更为灵活，服务提供方和调用方的依赖只依靠一纸契约, 
不存在代码级别的强依赖，这在强调快速演化的微服务环境下，显得更加合适。
<br><br>品牌机与组装机的区别
<br><br>社区支持与更新力度
<br><br>最为重要的是，DUBBO停止了5年左右的更新，虽然2017.7重启了。对于技术发展的新需求,需要由开发者自行拓展升级(比如当当网弄出了DubboX)，这对于很多想要采用微服务架构的中小软件组织，显然是不太合适的，中小公司没有这么强大的技术能力去修改Dubbo源码+周边的一整套解决方案,并不是每一个公司都有阿里的大牛 +真实的线上生产环境测试过。
<br><br> Dubbo的定位始终是一款RPC框架，而Spring Cloud的目标是微服务架构下的一站式解决方案。
<br><br>在面临微服务基础框架选型时Dubbo与Spring Cloud是只能二选一
## 版本说明
1. 希腊字母版本号
Base:设计阶段。只有相应的设计没有具体的功能实现。
Alpha:软件的初级版本。存在较多的bug
Bate:表示相对alpha有了很大的进步，消除了严重的bug，还存在一些潜在的bug。
Release:该版本表示最终版。
2. 版本发布计划说明

|版本号 |版本| 用途 |
|:--:|:--:|:--:|
|BUILD-XXX |开发版 | 一般是开发团队内部使用|
|GA | 稳定版| 内部开发到一定阶段了，各个模块集成后，经过全面测试发现没有问题，可对外发行了。这个时候叫GA (General Availability)。 基本.上可以使用了。|
|PRE(M1 M2)  |里程碑版 |由于GA还不属于 公开发行版，里面还有些功能不完善或者bug,于是就有了milestone (里程碑版)。milestone版主要修复了一些bug调整。一个GA后，一般会有多个里程本版。例如M1 M2 M3......|
|RC|候选发布版 |从BUILD后到CA在到M基本. 上系统就算定型了 ，这个时候系统就进入Release Candidate (候选发布版)。该阶段的软件类似于最终发行前的一个观察期，该期间只对一些发现的等级高的bug进行修复。发布RC1 RC2等版本|
|SR |正式发布版| 公开正式发布。正式发布版一般也有多个发布，例如SR1 SR2 SR3等等，一般是用来修复大bug或者优化|

## Spring Cloud Config Client 集中化管理集群配置
<br>技术回顾:回顾提及的Environment.以及Spring Boot配置相关的事件和监听器,
<br><br>如`ApplicationEnvironmentPreparedEvent`和`ConfigFileApplicationListener`，
<br><br>Bootstrap配置属性:解密Bootstrap配置属性与Spring Framework / Spring Boot配置架构的关系,介绍如何调整Bootstrap
配置文件路径、覆盖远程配置属性、自定义Bootstrap配置以及自定义Bootstrap配置属性源
<br><br>Environment端点:介绍/env端点的使用场景,并且解读其源码,了解其中奥秘
<br><br>安全:介绍客户端配置安全相关议题
## Spring / SpringBoot事件机制
### BootstrapApplicationListener
<br>1.负责加载`bootstrap.properties` 或者`bootstrap.yml`
<br><br>2.负责初始化Bootstrap ApplicationContext ID = "bootstrap"
```java
ConfigurableApplicationContext context = builder.run();
```
<br>Bootstrap是一个根Spring上下文，parent = null
<br><br>联想ClassLoader:
<br>ExtClassLoader <- AppClassLoader <- System ClassLoader -> BootstrapClassloader(null)
<br><br>加载的优先级高于 `ConfigFileApplicationListener`，所以`application.properties`文件即使定义也配置不到!
<br>原因在于:
`BootstrapApplicationListener`第六优先
`ConfigFileApplicationListener`第十一优先
<br><br>1.负责加载`bootstrap.properties` 或者 `bootstrap.yaml`
<br><br>2.负责初始化`Bootstrap ApplicationContext ID = "bootstrap"`
<br><br>Spring Cloud事件/监听器
<br>BootstrapApplicationListener
<br>Spring Cloud "/META-INF/spring.factories": 
```properties
# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.cloud.bootstrap.BootstrapApplicationListener,\
org.springframework.cloud.bootstrap.LoggingSystemShutdownListener,\
org.springframework.cloud.context.restart.RestartListener
```
### ConfigurableApplicationContext
<br>Bootstrap配置属性
<br><br>Bootstrap配置文件路径
`spring.cloud.bootstrap.location`
<br><br>覆盖远程配置属性
`spring.cloud.config.allowOverride`
<br><br>自定义Bootstrap配置
`@BootstrapConfiguration`
<br><br>自定义Bootstrap配置属性源
<br>PropertySourceLocator
## Bootstrap配置属性
### 理解Environment端点
<br>Env端点:`EnvironmentEndpoint`
`Environment`关联多个带名称的`PropertySource`
<br>可以参考一下SpringFramework源码:
`AbstractRefreshableWebApplicationContext`
```java
protected void initPropertySources() {
ConfigurableEnvironment env = this.getEnvironment();
if (env instanceof ConfigurableWebEnvironment) {
    ((ConfigurableWebEnvironment)env).initPropertySources(this.servletContext, this.servletConfig);
}

}
```
<br><br>Environment有两种实现方式:
<br>普通类型:  StandardEnvironment
<br>Web类型:  StandardServletEnvironment
<br><br>
<br>`Environment`关联着一个`PropertySources`实例
<br>`PropertySources`关联着多个`PropertySource`，并且有优先级
<br>其中比较常用的`PropertySource`实现:
<br>`Java System#getProperties`实现:  名称"systemProperties",对应的内容`System.getProperties()`
<br>`Java System#getenv`实现(环境变量) :  名称"systemEnvironment",对应的内容`System.getProperties()`
<br>关于Spring Boot优先级顺序，可以参考: https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-external-config
<br><br>实现自定义配置
<br><br>1.实现`PropertySourceLocator`
<br><br>2.暴露该实现作为一个Spring Bean
<br><br>3.实现`PropertySource`
<br><br>4.定义并且配置/META-INF/spring.factories
<br><br>注意事项:
<br> `Environment` 允许出现同名的配置，不过优先级高的胜出
<br>内部实现:  `MutablePropertySources`  关联代码:
```java
List<PropertySource<?>> propertySourceList = new CopyOnWriteArrayList<PropertySource<?>>();
```
<br>propertySourceList FIFO，它有顺序
<br>可以通过MutablePropertySources#addFirst提高到最优先，相当于调用:
<br>List#set(8, PropertySource); 
### 问题互动
<br>1. yml和.yaml是啥区别?
<br>答:  没有区别，就是文件扩展名不同
<br><br>2.自定义的配置在平时使用的多吗一般是什么场景
<br>答:  不多，一般用于中间件的开发
<br><br>3. /env端点的使用场景是什么
<br>答:用于排查问题，比如要分析@Value("${server.port}")里面占位符的具体值
<br><br>4. Spring cloud会用这个实现一个整合起来的高可用么
<br>答:  Spring Cloud整体达到一个目标， 把Spring Cloud的技术全部整合到一个项月，比如负载均衡、短路、跟踪、服务调用等
<br><br>5. 怎样防止Order一样
<br>答: Spring Boot和Spring Cloud里面没有办法，在Spring Security 通过异常实现的。
### Spring Cloud Config Server
<br>基本使用:介绍`@EnableConfigserver`、`Environment` 仓储、秘钥管理等基本使用方法
<br><br>分布式配置官方实现:介绍Spring官方标准分布式配置实现方式: Git实现和文件系统实现
<br><br>动态配置属性Bean :介绍`@RefreshScope`基本用法和使用场景,丢且说明其中的局限性。
<br><br>健康指标:介绍Spring Boot标准端口( `/health` )以及健康指标( Health Indicator)。
<br><br>分布式配置自定义实现:基于配置管理容器Zookeeper ,自定义实现分布式配置能力。
<br><br>健康指标自定义实现:实现分布式配置的健康指标自定义实现
### 介绍Environment仓储
<br>介绍Environment仓储概念
<br>{application}: 配置使用客户端应用名称
<br>{profile}:  客户端`spring.profiles.active`配置
<br>{label}:  服务端配置文件版本标识
<br>服务端配置
```properties
spring.cloud.config.server.git.uri
spring.cloud.config.server.git
```
<br>客户端配置
```properties
spring.cloud.config.uri
spring.cloud.config.name
spring.cloud.config.profile
spring.cloud.config.label
```
<br><br>Endpoints
<br>对于 Spring Boot Actuator application 可以使用一些额外的管理端点
<br>`/actuator/env ` to update the Environment and rebind `@ConfigurationProperties` and log levels.
<br>`/actuator/refresh` to re-load the boot strap context and refresh the `@RefreshScope` beans.
<br>`/actuator/restart` to close the ApplicationContext and restart it (disabled by default).
<br>`/actuator/pause` and `/actuator/resume` for calling the Lifecycle methods (stop() and start() on the ApplicationContext).
<br>If you disable the `/actuator/restart` endpoint then the `/actuator/pause `and `/actuator/resume` endpoints will also be 
<br>disabled since they are just a special case of `/actuator/restart`.

### 健康指标
<br>动态配置属性Bean
<br>@RefreshScope
<br>RefreshEndpoint
<br>ContextRefresher
<br><br>健康检查
<br>/health
<br>HealthEndpoint
<br>HealthIndicator
### 问题互动
<br>1.你们服务是基于啥原因采用的springboot的，这么多稳定性的问题?
<br> Spring Boot业界比较稳定的微服务中间件，不过它使用是易学难精!
<br><br>2.为什么要把配置项放到git上，为什么不放到具体服务的的程序里边; git在这里扮演什么样的角色;是不是和zookeeper一样
<br> Git文件存储方式、分布式的管理系统，Spring Cloud官方实现基于Git, 它达到的理念和ZK一样。
<br><br>3. 一个DB配置相关的bean用@RefreshScope修饰时，config service修改了db的配置,， 比如mysql的url,那么这个Bean会不会刷新?如果刷新了是不是获取新的连接的时候url就变了?
<br>如果发生了配置变更，我的解决方案是重启Spring Context。@RefreshScope 最佳实践用于配置Bean，比如:开关、阈值、文案等等
<br><br>4.如果这样是不是动态刷新就没啥用了吧
<br>不能一概而论，@RefreshScope 开关、阈值、文案等等场景使用比较多
### 其他内容
<br>REST API = /users , /withdraw
<br>HATEOAS= REST服务器发现的入口，类似UDDI (Universal Description Discoveryand Integration)  IHAL
<br>/users
<br>/withdraw
<br><br>Netty基于NIO
<br>NIO
<br>Reactor同步非阻塞-多工处理
<br>Reactive =
<br>Reactor +
<br>Asyn =
<br>异步非阻塞-多工处理
<br>Netty类似于Reactive
<br>观察者模式的实现
## Spring Cloud Netflix Eureka
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/19.png) 
<br>前微服务时代:介绍前微服务时代，服务发现和注册在SOA甚至是更早的时代的技术实现和实施方法,如WebService中的UDDI、REST 中的HEATOAS
<br><br>高可用架构:简介高可用架构的基本原则,计算方法和系统设计
<br><br>Eureka客户端:介绍Spring Cloud Discovery结合Netflix Eureka客户端的基本使用方法，包括服务发现激活、Eureka客户端注册配置以及API使用等
<br><br>Eureka服务器:介绍Eureka服务器作为服务注册中心的搭建方法,以及内建Dashboard基本运维手段
<br><br>规模大 强一致性 不适合Eureka 
<br><br>使用轮询(round-robin)负载算法的负载均衡器
### 前微服务时代
<br>前微服务时代分布式系统基本组成
<br>服务提供方( Provider)
<br>服务消费方( Consumer)
<br>服务注册中心( Registry) 
<br>服务路由( Router)
<br>服务代理 ( Broker )
<br>通讯协议( Protocol )
<br><br>通信协议
<br>Dubbo: Hession、Java Serialization (二进制)，跨语言不变，一般通过Client(Java、C++)
<br>二进制的性能是非常好：字节流，免去字符流(字符编码) ，机器友好、对人不友好
<br>序列化: 把编程语言数据结构转换成字节流、反序列化:字节流转换成编程语言的数据结构(原生类型的组合)
<br><br>URI:统一资源定位符
<br>URI用于网络资源定位的描述Universal Resource ldentifier
<br>URL: Universal Resource Locator
<br>网络是通讯方式
<br>资源是需要消费媒介
<br>定位是路由
<br><br>Proxy:一般性代理，路由
<br>Nginx:反向代理
<br>Broker:包括路由，并且管理，老的称谓( MOM )
<br>Message Broker:消息路由、消息管理(消息是否可达)
### 高可用架构
<br>1. 基本原则
<br>1) 消除单点失败
<br>2) 可靠性交迭
<br>3) 故障探测
<br><br>2. 可用性比率计算
<br>可用性比率:通过时间来计算(一年或者一月)
<br>比如:一年99.99%
<br>可用时间: 365 * 24 * 3600 * 99.99%
<br>不可用时间: 365 * 24 * 3600 * 0.01% = 3153.6秒 < 一个小时
<br>不可以时问: 1个小时推算一年 1 / 24 / 365 = 0.01 %
<br><br>3. 可靠性比率计算
<br>微服务里面的问题:
<br>一次调用:
<br>A->  B  -> C
<br>99%-> 99%-> 99%= 97%
<br>A->  B  -> C -> D
<br>99%-> 99%-> 99% -> 99% = 96%
<br><br>单台机器不可用比率:  1%
<br>两台机器不可用比率: 1% * 1%
<br>N机器不可用比率: 1%^ n
<br><br>结论:增加机器可以提高可用性，增加服务调用会降低可靠性，同时降低了可用性
### Eureka客户端
<br>传统技术
<br>WebService
<br>UDDI一REST
<br>HATEOAS
<br><br>Java
<br>JMS JNDI
<br><br>Spring Cloud-客户端
<br>Netfilx Eureka Client
### Eureka服务器
<br>Netfilx Eureka Server
<br>激活: @EnableEurekaServer
<br><br>Eureka服务器
<br>Eureka服务器一般不需要自我注册， 也不需要注册其他服务器
<br>Eureka自我注册的问题，服务器本身没有启动
<br><br>常用设计方式
<br>Fast Fail:快速失败
<br>Fault-Tolerance :容错
### 问题互动
<br>1.consul和Eureka是一样的吗
<br>提供功能类似，consul 功能更强大，广播式服务发现/注册
<br><br>2.重启eureka服务器，客户端应用要重启吗
<br>不用，客户端在不停地上报信息，不过在Eureka服务器启动过程中，客户单大量报错
<br><br>3.生产环境中，consumer是分别注册成多个服务，还是统一放在一 起注册成一个服务?权限应该如何处理?
<br>consumer 是否要分为多个服务，  要情况，大多数情况是需要，根据应用职责划分。权限根据服务方法需要，比如有些敏感操作的话，可以更具不同用户做鉴权。
<br><br>4.客户端上报的信息存储在哪里?内存中还是数据库中
<br>都是在内存里面缓存着，EurekaClient 并不是所有的服务，需要的服务。比如: Eureka Server管理了200个应用，每个应用存在100个实例，总体管理20000个实例。客户端更具自己的需要的应用实例。
<br><br>5. consumer调用Aprovider-a挂了，会自动切换Aprovider-b吗，保证请求可用吗
<br>当provider-a挂，会自动切换，不过不一定及时。不及时，服务端可能存在脏数据，或者轮训更新时间未达。
<br><br>6.一个业务中调用多个service时如何保证事务
<br>需要分布式事务实现(JTA)，可是一般互联网项目，没有这种昂贵的操作。
<br><br>7. Zookeeper保证CP
<br>当向注册中心查询服务列表时，我们可以容忍注册中心返回的是几分钟以前的注册信息，但不能接受服务直接down掉不可用。也就是说，服务注册功能对可用性的要求要高于一致性。 但是zk会出现这样-种情况，当master节点因为网络故障 与其他节点失去联系时，剩余节点会重新进行leader选举。问题在于,选举leader的时间太长，30 ~ 120s,且选举期间整个zk集群都是不可用的，这就导致在选举期间注册服务瘫痪。在云部署的环境下，因网络问题使得zk集群失去master节点是较大概率会发生的事，虽然服务能够最终恢复，但是漫长的选举时间导致的注册长期不可用是不能容忍的。
<br><br>8. Eureka保证AP
<br>Eureka看明白了这一点， 因此在设计时就优先保证可用性。`Eureka各个节点都是平等的`，几个节点挂掉不会影响正常节点的工作剩余的节点依然可以提供注册和查询服务。而Eureka的客户端在向某个Eureka注册或时如果发现连接失败，则会自动切换至其它节点，只要有一台Eureka还在， 就能保证注册服务可用(保证可用性),只不过查到的信息可能不是最新的(不保证强-致性)。 除此之外，Eureka还有一 种自我保护机制， 如果在15分钟内超过85%的节点都没有正常的心跳，那么Eureka就认为客户端与注册中心出现了网络故障，此时会出现以下几种情况:
<br><br>1) Eureka不再从注册列表中移除因为长时间没收到心跳而应该过期的服务
<br><br>2) Eureka仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上(即保证当前节点依然可用
<br><br>3) 当网络稳定时，当前实例新的注册信息会被同步到其它节点中
<br><br>因此，Eureka可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像zookeeper那样使整个注册服务瘫痪。
<br><br>9.  什么是自我保护模式
<br>1. 自我保护的条件
<br>一般情况下，微服务在Eureka.上注册后，会每30秒发送心跳包，Eureka 通过心跳来判断服务时候健康，同时会定期删除超过90秒没有发送心跳服务。
<br>2.  有两种情况会导致Eureka Server收不到微服务的心跳
<br>a.是微服务自身的原因
<br>b.是微服务与Eureka之间的网络故障
<br>通常(微服务的自身的故障关闭)只会导致个别服务出现故障，-般不会出现大面积故障，而(网络故障)通常会导致Eureka Server在短时间内无法收到大批心跳。
考虑到这个区别，Eureka设置了-一个阀值，当判断挂掉的服务的数量超过阀值时,Eureka Server 认为很大程度上出现了网络故障，将不再删除心跳过期的服务。
<br>3. 那么这个阀值是多少呢?
<br>5分钟之内是否低于85%;
<br>Eureka Server 在运行期间，会统计心跳失败的比例在15分钟内是否低于85%这种算法叫做Eureka Server 的自我保护模式。
<br><br>10. 为什么要启动自我保护
<br>1. 因为同时保留”好数据"与"坏数据"总比丢掉任何数据要更好，当网络故障恢复后，这个Eureka节点会退出”自我保护模式"。
<br>2. Eureka还有客户端缓存功能(也就是微服务的缓存功能)。即便Eureka集群中所有节点都宕机失效，微服务的Provider和Consumer都能正常通信。
<br>3. 微服务的负载均衡策略会自动剔除死亡的微服务节点。
<br><br>11. 为什么注册服务这么慢？
<br>作为实例还涉及到注册表（通过客户端serviceUrl）的定期心跳，默认持续时间为30秒。在实例，服务器和客户端在其本地缓存中都具有相同的元数据之前，
客户端无法发现服务（因此可能需要3次心跳）。您可以通过设置更改期间`eureka.instance.leaseRenewalIntervalInSeconds` 。
将其设置为小于30的值可加快使客户端连接到其他服务的过程。在生产中，最好坚持使用默认值，因为服务器中的内部计算会对租赁续订期做出假设。

12. 服务发现的好处

首先，它为应用团队提供了快速水平扩展的能力并减少在环境中运行的服务实例的数量，可以在池中添加或删除新的服务实例可用的服务。

第二个好处是它有助于提高应用程序的弹性 。当微服务实例变得不健康或不可用时，大多数服务发现引擎将从其内部可用服务列表中删除该实例。由于服务发现，下行服务造成的损害将最小化引擎将围绕不可用的服务路由服务。

高度可用 、对等、负载均衡、弹性、容错

13. 服务发现

在此模型中，当消费者需要调用服务时

1 它将联系服务发现服务以获取服务的所有服务实例消费者要求然后在服务消费者的本地缓存数据机。

2 每次客户想要调用服务时，服务使用者都会查找来自缓存的服务的位置信息。通常是客户端缓存将使用简单的负载平衡算法，如“循环”加载平衡算法，以确保服务调用分布在多个服务器上副案件。

3 然后，客户端将定期联系服务发现服务刷新其服务实例的缓存。客户端缓存最终是一致的，但是在客户联系服务的时候总是有风险用于刷新和调用的discovery实例，可以将调用指向服务器副作用不健康。
如果在调用服务的过程中，服务调用失败，则为本地服务发现缓存无效，服务发现客户端将尝试从服务发现代理刷新其条目。

14. 在Spring Cloud，Netflix Eureka和Netflix中使用了三种不同的机制Netflix Ribbon用于调用服务。

使用Spring Cloud服务DiscoveryClient

使用Spring Cloud和Ribbon支持的RestTemplate

使用Spring Cloud和Netflix的Feign客户端

15. 四种客户端弹性模式

1 客户端负载平衡

2 断路器

3 Fallbacks

4 Bulkheads

![客户端弹性模式](https://github.com/gaoyuanyuan2/distributed/blob/master/img/71.png) 

## Spring Cloud Netflix Ribbon
<br>Ribbon是一个客户端负载均衡器，可以让您对HTTP和TCP客户端的行为进行大量控制。Feign已经使用了Ribbon。如果您不想使用Eureka，Ribbon和Feign也可以使用。
<br><br>简介负载均衡客户端和服务端的相关理论,包括调度算法:如先来先服务、轮训、多级队列等。基本特性:非对称负载、健康检查、优先级队列等
<br><br>技术回顾:回顾Spring Framework HTTP组件RestTemplate的使用方法,结台ClientHttpRequestInterceptor实现简单负载均衡客户端
<br><br>Netflix Ribbon :作为Spring Cloud客户端负载均衡实现, Netflix Ribbon提供了丰富的组件,包括负载均衡器、负载均衡规则、PING 策略等,根据前章所积累的经验,实现客户端负载均衡
<br><br> Ribbon 提供云端负载均衡，有多种负载均衡策略可供选择，可配合服务发现和断路器使用。
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/24.png) 
<br><br>Ribbon在工作时分成两步
<br><br>第一步先选择EurekaServer ,它优先选择在同一个区域内负载较少的server.
<br><br>第二步再根据用户指定的策略，在从server取到的服务注册列表中选择一个地址。其中Ribbon提供了多种策略:比如轮询、随机和根据响应时间加权。
<br><br>官方文档明确给出了警告:
<br>这个自定义配置类不能放在`@ComponentScan`所扫描的当前包下以及子包下,否则我们自定义的这个配置类就会被所有的Ribbon客户端所共享，也就是说我们达不到特殊化定制的目的了。
### Eureka高可用
<br><br>1.  Eureka客户端高可用
<br>用域名方式最好，如果太多ip地址
<br><br>1)  高可用注册中心集群
<br>如果Eureka客户端应用配置多个Eureka注册服务器，那么默认情况只有第一台可用的服务器，存在注册信息。
<br>如果第一台可用的Eureka服务器Down掉了，那么Eureka客户端应用将会选择下一.台可用的Eureka服务器。
<br><br>配置源码( EurekaClientConfigBean )
<br>配置项`eureka.client.serviceUrl`实际映射的字段为serviceUr1,  它是Map类型，Key 为自定义，默认值"defaultZone", value 是需要配置的Eureka注册服务器URL。
```java
private Map<String, String> serviceUrl = new HashMap();
this.serviceUrl.put("defaultZone", "http://localhost:8761/eureka/");
```
<br>value可以是多值字段，通过"," 分割:
```java
String serviceUrls = (String)this.serviceUrl.get(myZone);
if (serviceUrls == null || serviceUrls.isEmpty()) {
serviceUrls = (String)this.serviceUrl.get("defaultZone");
}

if (!StringUtils.isEmpty(serviceUrls)) {
String[] serviceUrlsSplit = StringUtils.commaDelimitedListToStringArray(serviceUrls);
... 
}
```
<br><br>2)  获取注册信息时间间隔
<br>Eureka客户端需要获取Eureka服务器注册信息，这个方便服务调用。
<br>Eureka客户端:  EurekaClient ,关联应用集合:  Applications单个应用信息:  Application，关联多个应用实例单个应用实例:  `InstanceInfo` 
<br><br>当Eureka客户端需要调用具体某个服务时，比如`user-service-consumer`  调用`user-service-provider`，`user-service-provider`实际对应对象是`Application`,关联了许多应用实例( `InstanceInfo` )。
<br>如果应用`user-service-provider`的应用实例发生变化时，那么`user-service-consumer`是需要感知的。比如:  `user-service-provider`机器从10台降到了5台，那么，作为调用方的`user-service-consumer`需要知道这个变化情况。
<br>可是这个变化过程，可能存在一定的延迟，可以通过调整注册信息时间间隔来减少错误。

`EurekaClientConfigBean`
```java
private int registryFetchIntervalSeconds = 30;
private int instanceInfoReplicationIntervalSeconds = 30;
```
<br>具体配置项
```properties
##调整注册信息的获取周期，默认值:30秒
eureka.client.registryFetchIntervalSeconds = 10
```
<br><br>3)  实例信息复制时间间隔
<br>具体就是客户端信息的上报到Eureka服务器时间。当Eureka客户端应用上报的频率越频繁，  那么Eureka服务器的应用状态管理一致性就越高。
<br><br>具体配置项
```properties
##调整客户端应用状态信息上报的周期
eureka.client.instanceInfoReplicationIntervalSeconds = 5
```
<br>Eureka的应用信息同步的方式:拉模式
<br>Eureka的应用信息上报的方式:推模式
<br><br>4)  实例id 
<br>实例id
<br>从Eureka Server Dashboard里面可以看到具体某个应用中的实例信息，
<br>比如:
```properties
UP (2) -  192.168.1.103:user-service- provider:7075
192.168.1.103 :user- service- provider: 7078

```
<br><br>其中，它们命名模式:
`${hostname}:${spring.application.name}:${server.port}`
```properties
##Eureka应用实例的ID 
eureka.instance.instanceId =consumer:${server.port}
```
<br><br>5)  实例端点映射
`EurekaInstanceConfigBean`
```java
private String statusPageUrl;
```
<br>配置项
```properties
## Eureka 客户端应用实例状态URL
eureka.instance.statusPageUrlPath = /info
eureka.instance.healthCheckUrlPath = /health
```
<br>相互注册
<br>--spring.profiles.active=peer1
<br>Eureka Server1 里面的replicas信息:
<br>registered-replicas     http://localhost:9091/eureka/
<br>Eureka Server2 里面的replicas信息:
<br>registered-replicas     http://localhost:9090/eureka/
<br><br>通过
<br>--spring.profiles.active=peer1和
<br>--spring.profiles.active=peer2
<br>分别激活Eureka Server1和Eureka Server2
### RestTemplate
#### HTTP消息装换器: HttpMessageConverter
<br>自义定实现
<br>编码问题
#### HTTP Client适配.工厂: ClientHttpRequestFactory
<br>这个方面主要考虑大家的使用HttpClient偏好:
<br>Spring实现
<br>SimpleClientHttpRequestFactory
<br>HttpClient
<br>HttpComponentsClientHttpRequestFactory
<br>OkHttp
<br>OkHttp3ClientHttpRequestFactoryOkHttpClientHttpRequestFactory
<br><br>自义定实现
<br>切换序列化/反序列化协议
<br>举例说明:切换HTTP通讯实现，提升性能
```java
RestTemplate restTemplate = new RestTemplate(new HttpComponentsClientHttpRequestFactory()); // HTTP Client
```
#### HTTP请求拦截器: ClientHttpRequestInterceptor
加深RestTemplate拦截过程的理解
## 整合Netflix Ribbon 常见的负载均衡有软件Nginx, LVS, 硬件F5等。
<br>整合Netflix Ribbon
<br>RestTemplate增加一个LoadBalancerInterceptor，调用Netflix 中的LoadBalancer实现，  根据Eureka客户端应用获取月标应用lP+Port信息，轮训的方式调用。
<br>实际请求客户端
<br>LoadBalancerClient
<br>RibbonLoadBalancerClient
<br><br>负载均衡上下文
<br>LoadBalancerContext
<br>RibbonLoadBalancerContext
<br><br>核心规则接口
<br>IRule  尽量避免随机 无法把控
<br>随机规则: RandomRule
<br>最可用规则:  BestAvailableRule 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择-个并发量最小的服务
<br>轮训规则: RoundRobinRule
<br>重试实现: RetryRule 先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用的服务
<br>客户端配置: ClientConfigEnabledRoundRobinRule
<br>可用性过滤规则: AvailabilityFilteringRule 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务,还有并发的连接数量超过阈值的服务，然后对剩余的服务列表按照轮询策略进行访问
<br>RT权重规则: WeightedResponseTimeRule 根据平均响应时间计算所有服务的权重，响应时间越快服务权重越大被选中的概率越高。刚启动时如果统计信息不足，则使用RoundRobinRule策略， 等统计信息足够， 会切换到WeightedResponseTimeRule
<br>规避区域规则:  ZoneAvoidanceRule 默认规则，复合判断server所在区域的性能和server的可用性选择服务器
### 问题互动
<br>1.为什么要用eureka?
<br>目前业界比较稳定云计算的开发员中间件，虽然有一些不足，基本上可用
<br><br>2. eureka主要功能为啥不能用浮动ip代替呢?
<br>如果要使用浮动的IP，也是可以的，不过需要扩展
<br><br>3. eureka可以替换为zookeeper和consul那么这几个使用有什么差异?
<br>https://www.consul.io/intro/vs/zookeeper.html
<br>https://www.consul.io/intro/vs/eureka.html
<br><br>3.通讯不是指注册到defaultZone配置的那个么?
<br>默认情况是往defaultZone注册
<br><br>4.如果服务注册中心都挂了,服务还是能够运行吧?
<br>服务调用还是可以运行，有可能数据会不及时、不一致
<br><br>5. spring cloud日志收集有解决方案么?
<br>一般用HBase、  或者TSDB、elk
<br><br>https://github.com/OpenTSDB/opentsdb
##  Spring Cloud Hystrix
<br>核心理念:介绍服务短路的名词由来、目的,以及相关的类似慨念。随后讲述其中设计哲学、触发条件、处理手段以及客户端和服务端实现方法
<br><br>Spring Cloud Hystrix :作为服务端服务短路实现,介绍Spring Cloud Hytrix常用限流的功能,同时,说明健康指标以及数据指标在生产环境下的现实意义
<br><br>生产准备特性:介绍聚台数据指标Turbine、Turbine Stream ,以及整台Hystrix Dashboard
### 服务短路( CircuitBreaker )
<br>服务短路( CircuitBreaker )
<br>QPS: Query Per Second
<br>TPS: Transaction Per Second
<br>QPS:经过全链路压测，计算单机极限QPS，集群QPS=单机QPS*集群机器数量*可靠性比率
<br>全链路压测除了压极限QPS，还有错误数量
<br>全链路:一个完整的业务流程操作
<br>JMeter:  可调整型比较灵活

### Spring Cloud Hystrix Client
<br>官网:https://github.com/Netflix/Hystrix
<br>Reactive Java框架:
<br>Java 9 Flow API
<br>Reactor
<br>RxJava (Reactive X)
<br><br>激活Hystrix
<br>通过 `@EnableHystrix` 激活
<br>Hystrix配置信息wiki: https://github.com/Netflix/Hystrix/wiki/Configuration
<br><br>激活熔断保护
<br>@EnableCircuitBreaker激活:  @EnableHystrix + Spring Cloud功能
<br>@EnableHystrix激活，没有一些Spring Cloud功能，如/hystrix.stream端点
<br><br>Hystrix Endpoint( /hystrix.stream )
### Spring Cloud Hystrix Dashboard(不成熟)
<br>激活
<br>@EnableHystrixDashboard
<br><br>实心圆:共有两种含义。它通过颜色的变化代表了实例的健康程度，它的健康度从绿色<黄色<橙色<红色递减。
<br>该实心圆除了颜色的变化之外，它的大小也会根据实例的请求流量发生变化，流量越大该实心圆就越大。所以通过该实心圆的展示
就可以在大量的实例中快速的发现故障实例和高压力实例。
### 整合Netflix Turbine 
<br>数据聚合
### 问题互动
<br>1. ribbon是用来做什么的，只能负载均衡吗?
<br>主要用于客户端负载均衡
<br><br>2. Kafka与ActiveMQ ?
<br>ActiveMQ相对来比较完善的消息中问件，Kafka在能力上比较灵活，它放弃不少约束，性能相对比较好。
<br><br>3.要提高对java基础的提高有什么推荐的书籍吗
<br>其实基本经典的书要读，Java 编程思想、EffectiveJaval和2。相关类目，比如集合，要选择读。
<br><br>4.注释{@link怎么用，什么作用啊，怎么弄出来的，没看清楚
<br>JavaDoc一部分，通过Java注释生成HTML文档。
<br>{@link}引用到某个类，比如{@link String}
<br>@since从哪个版本开始
<br>@version表示当前版本
<br>@author作都
<br><code></code>里面嵌入Java代码
<br><br>5. spring cloud的config配置，获取到的git中 的properties文件的一些属性 (比如，my.name)，可以直接
<br>在其它spring的xmI中使用吗?需要怎么配.....
<br>利用注解`@ImportResource("abc.xml")`
<br>abc.xml:
```xml
<bean id="person" class="com.gupao.domain.Person">
<property name= "name" value="${my.name}" />
</bean>

```
<br>启动类Main.java
`@ImportResource( "abc . xm1")`
<br><br>6.将实时数据缓存Redis,
<br> Storm消费数据需要强持久性，Redis相对比DB逊色一点。
<br><br>7. spring boot中用new SpringApplicationBuilder().sources(AppCofig.class)方式启动，是先加载Appconfig还是先加载配置文件？
<br> AppConfig是一-个配置@Configration Class， 那么配置文件是一个外部资源，其实不会相互影响。如果AppConfig增加了@PropertySource或者@PropertySources的话，会优先加载@PropertySource中的配置资源。
<br><br>8.使用超时机制、服务降级
<br>服务降级服务调用接口，如果发生错误或者超时，不让调用接口，调用本地fallback
<br>服务雪崩产生服务堆积，导致其他服务接口无法。
<br><br>9. 服务雪崩 雪崩效应，所有请求在处理一个服务，不能访问其他服务接口
<br>多个微服务之向调用的吋候,假设微服务A调用微服务B和微服务C,微服务B和微服务C又调用其它的微服务,这就是所謂的`扇出` 。如果扇出的链路上某个微服务的调用响应时间过长或者不可用,对微服务A的调用就会占用越来越多的系统资源,进而引起系
统崩溃,所渭的“雪崩效应”
<br>对于高流量的座用来说,单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和.比失敗更糟糕的是,这些应用程序还可能导致服务之同的延迟増加,备份队列,线程和其他系统资源紧张,导致整个系统发生更的级联故障,
这些都表示需要对故障和延时逃行隔离和管理,以便単个依赖关系的失败,不能取消整个应用程序或系统
<br><br>10.如何解决服务雪崩效应
<br>1) 超时机制--服务降级处理
<br>2) 服务降级服务接口发生错误，不去调用接口，调用本地方法fallback
<br>3) 熔断机制类似保险丝 熔断机制就是为解决服务高并发，一旦达到规定请求，熔断，报错。----服务降级
<br>4) 隔离机制---每个服务接口隔离开;
<br><br>5） 限流机制nginx使用网关
<br><br>11. SpringCloud hystrix断路器pc远程调用，解决服务雪崩效
<br>服务与服务之间报错信息。
<br> hystrix断路器服务降级、熔断机制、隔离资源。
<br><br>12. “断路器”本身是一种开关装置
<br>1) 当某个服务单元发生故障之后，通过断路器的故障监控(类似熔断保险丝) ,向调用方返回一个符合预期的、可处理的备选响应(FallBack) ,
而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、
不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。
<br>2) 服务熔断:服务端。
<br>一般是某个服务故障或者异常引起,类似现实世界中的“保险丝“，当某个异常条件被触发，直接熔断整个服务，而不是一直等到此服务超时。
<br>熔断机制是应对雪崩效应的一种微服务链路保护机制。
<br>当扇出链路的某个微服务不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回"错误”的响应信息。
<br>当检测到该节点微服务调用响应正常后恢复调用链路。在SpringCloud框架里熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内20次调用失败就会启动熔断机制。熔断机制的注解是@HystrixCommand.
<br><br>7. 服务降级: 客户端
<br>服务降级处理是在客户端实现完成的，与服务端没有关系。整体资源快不够了，忍痛将某些服务先关掉，待渡过难关，再开启回来。
<br>所谓降级，一般是从整体负荷考虑。就是当某个服务熔断之后，服务器将不再被调用，
此时客户端可以自己准备-一个本地的fallback回调，返回一个缺省值。
这样做，虽然服务水平下降，但好歹可用，比直接挂掉要强。
## Spring Cloud Feign
## 官方解释
Feign是一个声明性的Web服务客户端。它使编写Web服务客户端变得更容易。要使用Feign，请创建一个界面并对其进行注释。它具有可插入的注释支持
，包括Feign注释和JAX-RS注释。Feign还支持可插拔编码器和解码器。Spring Cloud增加了对Spring MVC注释的支持，并使用了HttpMessageConverters Spring Web中默认使用的注释。
Spring Cloud集成了Ribbon和Eureka，可在使用Feign时提供负载均衡的http客户端。
### Feign能干什么
<br>1.Feign旨在使编写Java Http客户端变得更容易。
<br>前面在使用Ribbon+ RestTemplate时，利用RestTemplate对http请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用， 所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装， 由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下，我们只需创建一个接口并使用注解的方式来配置它(以前是Dao接口. 上面标注Mapper注解，现在是一个微服务接口上面标注一 个Feign注解即可)， 即可完成对服务提供方的接口绑定，简化了使用Spring cloud Ribbon时， 自动封装服务调用客户端的开发量。
<br><br>2. Feign集成了Ribbon
<br>利用Ribbon维护了MicroServiceCloud-Dept的服务列表信息，并且通过轮询实现了客户端的负载均衡。而与Ribbon不同的是，通过feign只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用
### Feign基本使用
<br>申明式Web服务客户端: Feign
<br>申明式:接口声明、Annotation 驱动
<br>Web服务:HTTP的方式作为通讯协议
<br>客户端:  用于服务调用的存根
<br>Feign:  原生并不是Spring Web MVC的实现，基于AX-RS (Java REST规范)实现。Spring Cloud封装了Feign，
<br>使其支持Spring Web MVC。RestTemplate 、HttpMessageConverter
<br><br>RestTemplate以及Spring Web MVC可以显示地自定义HttpMessageConverter 实现。
<br><br>假设，有一个ava接口PersonService , Feign可以将其声明它是以HTTP方式调用的。
<br><br>需要服务组件(SOA)
<br>1.注册中心( Eureka Server) :服务发现和注册
<br>2. Feign客户(服务消费)端:调用Feign 中明接口
<br>3. Feign服务(服务提供)端:不一定强制实现Feign申明接口
<br>4. Feign声明接口(契约) :定义一种Java强类型接口
<br><br>需要服务组件(SOA) :
<br>1.注册中心(Eureka Server) :服务发现和注册
<br>应用名称: Spring-cloud-eureka-server.服务端口: 12345
<br><br>2. Feign声明接口(契约) :定义一种Java强类型接口
<br> person-api
<br><br>3. Feign客户(服务消费)端:调用Feign 中明接口
<br>应用名称: person-client
<br><br>4. Feign服务(服务提供)端:不一定强制实现Feign中明接口
<br>应用名称: person-service
<br><br>Feign客户(服务消费)端、Feign 服务(服务提供)端以及Feign声明接口(契约)存放在同一个工程目录。
<br><br>调用顺序
<br>PostMan -> person-client -> person-service
<br>person-api定义了@FeignClients(value="person-service"), person-service实际是一个服务器提供方的应用名称。
<br>person-client和person-service两个应用注册到了Eureka Server
<br>person-client可以感知person-service应用存在的，并且Spring Cloud帮助解析PersonService 中声明的应用名称: "person-service",
<br>因此person-client在调用心PersonService、服务时，实际就路由到person-service的URL
<br><br>整合Netflix Ribbon
<br>官方参考文档: http://cloud.spring.io/spring-cloud-static/Dalston.SR4/single/spring-cloud.html#spring-cloud-ribbon
<br><br>关闭Eureka注册
<br>调整person-client
### Hystrix整合
`注意: Hystrix 可以是服务端实现，也可以是客户端实现，类似于AOP封装:正常逻辑、容错处理。`
### 问题互动
<br>1.能跟dubbo一样， 消费端像调用本地接口方法一样调用服务端提供的服务么?还有就是远程调用方法参数对象不用实现序列化接口么?
<br> FeignClient 类似Dubbo，不过需要增加以下@Annotation，和调用本地接口类似
<br><br>2. Feign通过注释驱动弱化了调用Service细节，但是Feign的Api设定会暴露service地址，那还有实际使用价值么?
<br>实际价值是存在的，Feign API暴露URI，比如: "/person/save"
<br><br>3.整合ribbon不是一定要关闭注册中心吧?
<br> Ribbon 对于Eureka是不强依赖，不过也不排除
<br><br>4.生产环境上也都是feign的?
<br>据我所知，不少的公司在用，需要Spring Cloud更多整合:Feign作为客户端
<br>Ribbon作为负载均衡 Ribbon默认是轮询
<br>Eureka作为注册中心
<br>Zuul作为网管
<br>Security作为安全OAuth 2认证
<br><br>5. Ribbon直接配置在启动类上是作用所有的controller,那如果想作用在某个呢?
<br> Ribbon 是控制全局的负载均衡，主要作用于客户端Feign，Controller 是调用Feign接口，可能让人感觉直接作用了Controller。
<br><br>6.其实eureka也有ribbon中简单的负载均衡吧
<br> Eureka 也要Ribbon的实现，可以参考`com.netflix.ribbon:ribbon-eureka`
<br><br>7.如果服务提供方，没有接口，我客户端一般咋处理?要根据服务信息，自建feign接口?
<br>当然可以，可是Feign的接口定义就是要求强制实现
<br><br>8.无法连接注册中心的老服务，如何调用cloud服务
<br>可以通过域名的配置Ribbon服务白名单
<br><br>9. eureka有时监控不到宕机的服务正确的启动方式是什么
<br>这可以调整的心跳检测的频率

## Spring Cloud Zuul
### 身份验证、压力测试、金丝雀测试、动态路由、服务迁移、减载、安全、静态响应处理、主动/主动交通管理。（处理静态文件不行） 代理+路由+过滤 可以解决跨域问题
<br>Nginx + Lua
<br>控制规则(A/B Test)
### 整合Ribbon
### 整合Eureka
### 整合Hystrix
### 整合Feign
### 整合Config Server
<br>前面的例子展示Zuul、Hystrix 、 Eureka以及Ribbon能力，可是配置相对是固定，真实线上环境需要-一个动态路由，即需要动态配置。
<br>git init
<br>git add
<br>git commit
<br>zk比git好用
### 问答
<br>1.看下来过程是:通过url去匹配zuul中配置的serviceld然后没整合ribbon时， 直接去eureka中找服务实例去调用， 
如果整合了ribbon时，直接从listofService中取得一个实例，然后调用返回，对不?
<br>大致上可以这么理解，不过对应的listOfServicers不只是单个实例，而可能是一个集群，主要可以配置域名。
<br><br>2.为什么要先调用client而不直接调用server,还是不太理解
<br>这个只是一个演示程序，client 在正式使用场景中，并不是一简单的调用，它可能是一个聚合服务
<br><br>3. zuul是不是更多的作为业务网关
<br>是的，很多企业内部的服务通过Zuul做个服务网关
<br><br>4.渡劫`RequestContex`t经存在`ThreadLocal`中了，为什么还要使用`ConcurrentHashMap`?
<br>`ThreadLocal`只能管当前线程，不能管理子线程，子线程需要使用`InheritableThreadLocal`。  `ConcurrentHashMap`实现一下，
如果上下文处于多线程线程的环境，比如传递到子线程。比如: T1在管理`RequestContext`,
但是T1又创建了多个线程(t1、t2)，这个时候，把上下文传递到了子线程t1和t2.
<br>Java的进程所对应的线程main线程( group:  main)，main线程是所有子线程的父线程，main线程T1，T1又可以创建t1和t2
```java
public abstract class RequestContextHolder {
private static final boolean jsfPresent = 
ClassUtils.isPresent("javax.faces.context.FacesContext", RequestContextHolder.class.getClassLoader());
private static final ThreadLocal<RequestAttributes> requestAttributesHolder =
 new NamedThreadLocal("Request attributes");
private static final ThreadLocal<RequestAttributes> inheritableRequestAttributesHolder =
 new NamedInheritableThreadLocal("Request context");
...
}
```
<br><br>5. `ZuulServlet`经管理了`RequestContext`的生命周期了，为什么`ContextLifecycleFilter`还要在做一遍?
<br>`ZuulServelt`最终也会清理掉`RequestContext`
```java
finally {
RequestContext.getCurrentContext( ).unset( ) ;
}
```
<br>为什么 `ContextLifecycleFilter`也这么干?
```java
finally {
RequestContext.getCurrentContext( ).unset( );
}
```
<br>不要忽略了`ZuulServletFilter`， 也有这个处理:
```java
finally {
RequestContext.getCurrentContext().unset( );
}
```
RequestContext是任何Servlet或者Filter都能处理，  那么为了防止不正确的关闭，那么 `ContextLifecycleFilter`
<br>相当于兜底操作，就是防止ThreadLocal没有被remove掉。
<br><br>6. ThreadLocal对应了一个Thread,那么是不是意味着者Thread处理完了，那么ThreadLocal也随之GC?
<br>所有Servlet均采用线程池，因此，不清空的话，可能会出现意想不到的情况。除非，每次都异常!(这种情况也要依赖于线程池的实现)

### 动态路由
<br>动态路由需要达到可持久化配置，动态刷新的效果。如架构图所示，不仅要能满足从spring的配置文件properties加载路由信息，
还需要从数据库加载我们的配置。另外一点是，路由信息在容器启动时就已经加载进入了内存，我们希望配置完成后，实施发布，动态刷新内存中的路由信息，达到不停机维护路由信息的效果。

[动态路由](https://dzone.com/articles/persistent-and-fault-tolerant-dynamic-routes-using)
## Spring Cloud Stream
### Kafka绑定实现
<br>1.  用途
<br>消息中间件
<br>流式计算处理
<br>日志
<br><br>Spring Kafka
<br>Spring Boot Kafka
<br>Spring Cloud Stream
<br>Spring Cloud Stream Kafka Binder
<br><br>Publish/Source  Subscriber/Sink
<br><br>同类产品比较
<br>ActiveMQ: JMS (Java Message Service )  规范实现
<br>RabbitMQ: AMQP ( Advanced Message Queue Protocol)规范实现
<br>Kafka:并非某种规范实现，它灵活和性能相对是优势
<br><br>设计模式
<br>Spring社区对data( spring-data)操作，有一个基本的模式，Template 模式:
<br>JDBC : JdbcTemplateRedis: RedisTemplate
<br>Kafka : KafkaTemplate
<br>JMS :  JmsTemplate
<br>Rest:  RestTemplate
<br>XXXTemplate定 实现xxxOperations
<br><br>Spring Cloud Stream
<br>基本概念
<br>Source:  来源，近义词: Producer、 Publisher
<br>Sink:  接收器，近义词:  Consumer、Subscriber
<br>Processor:  对于上流而言是Sink,对于下流而言是Source
<br><br>Reactive Streams :
<br>Publisher
<br>Subscriber
<br>Processor
<br><br>消息大致分为两个部分:
<br>消息头(Headers)
<br>消息体( Body/Payload )
### RabbitMQ 实现
### 问答部分
<br><br>1.  当使用Future时， 异步调用都可以使用get()方式强制执行吗?
<br>是的，  get()等待当前线程执行完毕，并且获取返回接口
<br><br>2.  `@KafkaListener`和KafkaConsumer 有啥区别
<br>没有实质区别，主要是编程模式。
<br>@KafkaListener采用注解驱动
<br>KafkaConsumer采用接口编程
<br><br>3.  消费者接受消息的地方在哪?
<br>订阅并且处理后，就消失。
<br><br>4.  在生产环境配置多个生产者和消费者只需要定义不同的group就可以了吗?
<br>group是一种，要看是不是相同Topic
<br><br>5.  为了不丢失数据，  消息队列的容错，和排错后的处理，如何实现的?
<br>这个依赖于zookeeper
<br><br>6.  异步接受除了打印还有什么办法处理消息吗
<br>可以处理其他逻辑，比如存储数据库
<br><br>7.  `@EnableBinding`有什么用?
<br>`@EnableBinding`将Source、 Sink 以及Processor 提升成相应的代理
<br><br>8.  `@Autowired` Source source
<br>这种写法是默认用官方的实现?答:是官方的实现
<br><br>9.  `@EnableBinder` ,  `@EnableZuulProxy`，`@EnableDiscoverClient`这 些注解都是通过特定BeanPostProcessor实现的吗?
<br>不完全对，主要处理接口在@Import
<br>ImportSelector实现类
<br>ImportBeanDefinitionRegistrar实现类。`@Configuration` 标注类
<br>BeanPostProcessor  实现类
<br><br>10. 我对流式处理还是懵懵的到底啥是流式处理怎样才能称为流式处理一般应用在什么场景?
<br>Stream 处理简单地说，异步处理，消息是一种处理方式。
<br>提交中请，机器生成，对于高密度提交任务，多数场景采用异步处理，Stream. Event-Driven。举例说明:审核流程，鉴别黄图。
<br><br>11. 如果是大量消息怎么快速消费用多线程吗?
<br>确实是使用多线程，不过不一定奏效。依赖于处理具体内容，比如: 一个线程使用了25% CPU，四个线程就将CPU耗尽。因此，并发100个处理，实际上，还是4个线程在处理。1/O密集型、CPU密集型。
<br><br>12. 如果是大量消息怎么快速消费用多线程吗
<br>大多数是多线程，其实也单线程，流式非阻塞。
<br><br>13. 购物车的价格计算可以使用流式计算来处理么?能说下思路么?有没有什么高性能的方式推荐?
<br>当商品添加到购物车的时候，就可以开始计算了。
## Spring Cloud Bus
## Spring Cloud Sleuth 实现了分布式跟踪解决方案
<br>Span(跨度):工作的基本单位。例如，发送RPC是一个新的span，就像向RPC发送响应一样。span由一个惟一的64位ID标识，另一个64位ID标识，用于跟踪span所在的位置。span还有其他数据，比如描述、时间戳事件、键值注释(标记)
<br><br>Trace(痕迹):形成树状结构的一系列跨度。例如，如果您运行分布式大数据存储，则可能会由PUT请求形成跟踪。
、导致它们的span的ID和进程ID(通常是IP地址)。
<br>日志发生的变化
<br>当应用ClassPath下存在`org.springframework.cloud:spring-cloud-starter-sleuth`时候，日志会发生调整。
<br>调整当前日志系统(slf4j) 的MDC
<br>MDC : Mapped Diagnostic Context
`spring-cloud-starter-sleuth`会自动装配一个名为TraceFilter组件，它公增加一些slf4j MDC
<br>跟踪 排查问题 性能考察
### 分布式应用跟踪。ZisKin整合
### 问答
<br>1.  SpringBoot 和springCloud区别
<br>Spring Framework,封装了大多数Java EE标准技术以及开源实现方法，提高生成力。不过Spring应用需要配置的相关组件，Spring Boot帮助简化了配置步骤，采用默认的配置能够快速的启动或者构建一个Java EE应用。Spring Boot单机应用，Spring
Cloud是将Spring Boot变成云应用，那么里面需要增强一些组件，监控、限流、服务注册和发现等等。
<br><br>2.  SpringData-jpa和JTA持久化框架区别
<br>JIA 主要关注分布式事务，事务操作背后可能资源是数据库、消息、或者缓存等等。
从数据库角度，JTA使用JPA作为存储，但是可以使用JDBC。JTA还能操作JMS。
<br><br>3.  feign和ribbon区别?feign内部实现依靠的是ribbon的嘛?
<br>Feign 作为声明式客户端调用，Ribbon 主要负责负载均衡。Feign 可以整合Ribbon,  但是不是强依赖。Spring Cloud对Feign增强，Feign 原始不支持SpringWebMVC,  而是支持标准JAX-RS(Rest标准)
<br><br>4.  整合图，zuul换成nginx，nginx应该怎么配置才能使用sleuth,从网关开始监控?
<br>nginx 需要增加HTTP.上报监控信息到zipkin Server
<br><br>5.  SpringCloud服务治理能和dubbo共存，或者替换成dubbo吗
<br>这个问题在社区里面有人问题，月前暂时没有确定答复。
<br><br>6.  想和mybatis-样，可以根据条件生成不同的sql?
<br>Spring Data IPA不太好实现，满足90%的CRUD需求。
<br><br>7.  spring-data-jpa-reactive里面的实现为什么方法的返回值不能使用Page了?比如接口只能声明Flux<T> findAl(Pageable pageable)而不能使用Page<T> findAll
<br>(Pageable pageable)或者Mono<Page<T>> findAll(Pageable pageable)
Reactive是推模式，所以被动更新。Page 是Iterable接口，它是拉的模式，如果在reactive中返回Page，那么违反了设计的初衷。
<br><br>8.  spring boot和spring cloud,生成环境怎么部署比较好，是直接java运行还是放到tomcat中间件里统一启动?
<br>直接通过java或者jar命令启动
<br><br>9.  使用spring cloud这一套 框架，怎么进行API鉴权，认证成功的用户信息怎么保存，微服务的数据状态又怎么保存
<br>Spring Security QAuth2验证，Spring Session管理用户状态，会话状态，不需要长期保存，在短时间内保存，  比如Spring Session + Redis (30分钟)。
<br><br>10.  Spring Security为什么不能跨版本使用
<br>Spring Security兼容不是特别好，一般建议统一版本。
<br><br>11.  Shiro和Spring Security的区别?
<br>Shiro是纯后端的安全校验和认证框架，SpringSecurity是前端+后端安全校验和认证框架。
<br><br>12.  POJO可以和IPA解耦么，  annotation往往还得引入hb?除了xml配置。耦合得有点闹心啊?
<br>所以应该把DTO对象和Entity解耦，不要继承也不要组合。
<br><br>13.  说说你怎么区别spring ( 包括最近讲的)里大量注解的理解和使用?
<br>作用域来区别，职责。
<br>数据: JDBC、JPA、Cache、NoSQL、Message安全:认证和鉴别
<br>监控:数据埋点、数据收集
<br>Web:. Servlet、 Spring WebMVC、JAX-RS、 WebSocket、 WebServices
<br>配置: System Properties、Properties、 YAML、启动参数、CSV、XML




