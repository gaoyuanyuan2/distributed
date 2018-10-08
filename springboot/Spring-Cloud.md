# Spring Cloud
## Spring Cloud Config Client
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
<br>前微服务时代:介绍前微服务时代，服务发现和注册在SOA甚至是更早的时代的技术实现和实施方法,如WebService中的UDDI、REST 中的HEATOAS
<br><br>高可用架构:简介高可用架构的基本原则,计算方法和系统设计
<br><br>Eureka客户端:介绍Spring Cloud Discovery结合Netflix Eureka客户端的基本使用方法，包括服务发现激活、Eureka客户端注册配置以及API使用等
<br><br>Eureka服务器:介绍Eureka服务器作为服务注册中心的搭建方法,以及内建Dashboard基本运维手段
<br><br>规模大 强一致性 不适合Eureka
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
## Spring Cloud Netfix Ribbon
<br>简介负载均衡客户端和服务端的相关理论,包括调度算法:如先来先服务、轮训、多级队列等。基本特性:非对称负载、健康检查、优先级队列等
<br><br>技术回顾:回顾Spring Framework HTTP组件RestTemplate的使用方法,结台ClientHttpRequestInterceptor实现简单负载均衡客户端
<br><br>整合Netlix Ribbon :作为Spring Cloud客户端负载均衡实现, Netflix Ribbon提供了丰富的组件,包括负载均衡器、负载均衡规则、PING 策略等,根据前章所积累的经验,实现客户端负载均衡
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
## 整合Netflix Ribbon
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
<br>最可用规则:  BestAvailableRule
<br>轮训规则: RoundRobinRule
<br>重试实现: RetryRule
<br>客户端配置: ClientConfigEnabledRoundRobinRule
<br>可用性过滤规则: AvailabilityFilteringRule
<br>RT权重规则: WeightedResponseTimeRule
<br>规避区域规则:  ZoneAvoidanceRule
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
### 整合Netflix Turbine 
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
## Spring Cloud Feign
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
<br>Ribbon作为负载均衡
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
### 身份验证、压力测试、金丝雀测试、动态路由、服务迁移、减载、安全、静态响应处理、主动/主动交通管理。（处理静态文件不行）
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
## Spring Cloud Sleuth
### 分布式应用跟踪。ZisKin整合




