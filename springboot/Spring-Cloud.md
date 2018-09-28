## Spring Cloud
#### Spring Cloud Config Client
<br>技术回顾:回顾提及的Environment.以及Spring Boot配置相关的事件和监听器,
<br><br>如`ApplicationEnvironmentPreparedEvent`和`ConfigFileApplicationListener`，
<br><br>Bootstrap配置属性:解密Bootstrap配置属性与Spring Framework / Spring Boot配置架构的关系,介绍如何调整Bootstrap
配置文件路径、覆盖远程配置属性、自定义Bootstrap配置以及自定义Bootstrap配置属性源
<br><br>Environment端点:介绍/env端点的使用场景,并且解读其源码,了解其中奥秘
<br><br>安全:介绍客户端配置安全相关议题
#### Spring / SpringBoot事件机制
##### BootstrapApplicationListener
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
##### ConfigurableApplicationContext
<br>Bootstrap配置属性
<br><br>Bootstrap配置文件路径
`spring.cloud.bootstrap.location`
<br><br>覆盖远程配置属性
`spring.cloud.config.allowOverride`
<br><br>自定义Bootstrap配置
`@BootstrapConfiguration`
<br><br>自定义Bootstrap配置属性源
<br>PropertySourceLocator
#### Bootstrap配置属性
#### 理解Environment端点
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
#### 问题互动
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
#### Spring Cloud Config Server
<br>基本使用:介绍`@EnableConfigserver`、`Environment` 仓储、秘钥管理等基本使用方法
<br><br>分布式配置官方实现:介绍Spring官方标准分布式配置实现方式: Git实现和文件系统实现
<br><br>动态配置属性Bean :介绍`@RefreshScope`基本用法和使用场景,丢且说明其中的局限性。
<br><br>健康指标:介绍Spring Boot标准端口( `/health` )以及健康指标( Health Indicator)。
<br><br>分布式配置自定义实现:基于配置管理容器Zookeeper ,自定义实现分布式配置能力。
<br><br>健康指标自定义实现:实现分布式配置的健康指标自定义实现

##### 介绍Environment仓储
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

##### 健康指标
<br>动态配置属性Bean
<br>@RefreshScope
<br>RefreshEndpoint
<br>ContextRefresher
<br><br>健康检查
<br>/health
<br>HealthEndpoint
<br>HealthIndicator
##### 问题互动
<br>1.你们服务是基于啥原因采用的springboot的，这么多稳定性的问题?
<br> Spring Boot业界比较稳定的微服务中间件，不过它使用是易学难精!
<br><br>2.为什么要把配置项放到git上，为什么不放到具体服务的的程序里边; git在这里扮演什么样的角色;是不是和zookeeper一样
<br> Git文件存储方式、分布式的管理系统，Spring Cloud官方实现基于Git, 它达到的理念和ZK一样。
<br><br>3. 一个DB配置相关的bean用@RefreshScope修饰时，config service修改了db的配置,， 比如mysql的url,那么这个Bean会不会刷新?如果刷新了是不是获取新的连接的时候url就变了?
<br>如果发生了配置变更，我的解决方案是重启Spring Context。@RefreshScope 最佳实践用于配置Bean，比如:开关、阈值、文案等等
<br><br>4.如果这样是不是动态刷新就没啥用了吧
<br>不能一概而论，@RefreshScope 开关、阈值、文案等等场景使用比较多
##### 其他内容
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
#### Spring Cloud Netflix Eureka
<br>前微服务时代:介绍前微服务时代，服务发现和注册在SOA甚至是更早的时代的技术实现和实施方法,如WebService中的UDDI、REST 中的HEATOAS
<br><br>高可用架构:简介高可用架构的基本原则,计算方法和系统设计
<br><br>Eureka客户端:介绍Spring Cloud Discovery结合Netflix Eureka客户端的基本使用方法，包括服务发现激活、Eureka客户端注册配置以及API使用等
<br><br>Eureka服务器:介绍Eureka服务器作为服务注册中心的搭建方法,以及内建Dashboard基本运维手段
#### Spring Cloud Netfix Ribbon
<br>简介负载均衡客户端和服务端的相关理论,包括调度算法:如先来先服务、轮训、多级队列等。基本特性:非对称负载、健康检查、优先级队列等
<br><br>技术回顾:回顾Spring Framework HTTP组件RestTemplate的使用方法,结台ClientHttpRequestInterceptor实现简单负载均衡客户端
<br><br>整合Netlix Ribbon :作为Spring Cloud客户端负载均衡实现, Netflix Ribbon提供了丰富的组件,包括负载均衡器、负载均衡规则、PING 策略等,根据前章所积累的经验,实现客户端负载均衡
####  Spring Cloud Hystrix
<br>核心理念:介绍服务短路的名词由来、目的,以及相关的类似慨念。随后讲述其中设计哲学、触发条件、处理手段以及客户端和服务端实现方法
<br><br>Spring Cloud Hystrix :作为服务端服务短路实现,介绍Spring Cloud Hytrix常用限流的功能,同时,说明健康指标以及数据指标在生产环境下的现实意义
<br><br>生产准备特性:介绍聚台数据指标Turbine、Turbine Stream ,以及整台Hystrix Dashboard


