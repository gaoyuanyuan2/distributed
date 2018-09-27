## Spring Cloud

#### Spring Cloud Config Client
<br>技术回顾:回顾提及的Environment.以及Spring Boot配置相关的事件和监听器,
<br><br>如`ApplicationEnvironmentPreparedEvent`和`ConfigFileApplicationListener`，
<br><br>Bootstrap配置属性:解密Bootstrap配置属性与Spring Framework / Spring Boot配置架构的关系,介绍如何调整Bootstrap配置文件路径、
覆盖远程配置属性、自定义Bootstrap配置以及自定义Bootstrap配置属性源
<br><br>Environment端点:介绍/env端点的使用场景,并且解读其源码,了解其中奥秘
<br><br>安全:介绍客户端配置安全相关议题

#### 主要议题
#### Spring技术体系
#### Spring / SpringBoot事件机制
##### BootstrapApplicationListener
<br>1.负责加载`bootstrap.properties` 或者`bootstrap.yml`
<br><br>2.负责初始化Bootstrap ApplicationContext ID = "bootstrap"
```java
ConfigurableApplicationContext context = builder.run();
```
<br><br>Bootstrap是一个根Spring上下文，parent = null
<br><br>联想ClassLoader:
<br>ExtClassLoader <- AppClassLoader <- System ClassLoader -> BootstrapClassloader(null)
<br><br>加载的优先级高于 `ConfigFileApplicationListener`，所以`application.properties`文件即使定义也配置不到!
<br>原因在于:
`BootstrapApplicationListener`第六优先
`ConfigFileApplicationListener`第十一优先
<br><br>1.负责加载`bootstrap.properties` 或者 `bootstrap.yaml`
<br><br>2.负责初始化`Bootstrap ApplicationContext ID = "bootstrap"`
<br><br>pring Cloud事件/监听器
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
<br>关于Spring Boot优先级顺序，可以参考: 考: https://docs.spring.io/spring-boot/docs/2.0.0.BUILD-SNAPSHOT/reference/htmlsingle/#boot-features-external-config
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
<br><br>propertySourceList FIFO，它有顺序
<br>可以通过MutablePropertySources#addFirst提高到最优先，相当于调用:
<br>List#set(8, PropertySource); 
#### 问题互动
<br>1. yml和.yaml是啥区别?
<br>答:  没有区别，就是文件扩展名不同
<br><br>2.自定义的配置在平时使用的多吗一般是什么场景
<br>答:  不多，一般用于中间件的开发
<br><br>3. /env端点的使用场景是什么
<br>答:用于排查问题，比如要分析@Value("${server . port}")里面占位符的具体值
<br><br>4. Spring cloud会用这个实现一个整合起来的高可用么
<br>答:  Spring Cloud整体达到一个月 标，  把Spring Cloud的技术全部整合到一个项月，比如负载均衡、短路、跟踪、服务调用等
<br><br>5. 怎样防止Order一样
<br>答: Spring Boot和Spring Cloud里面没有办法，在Spring Security 通过异常实现的。
#### Spring Cloud Config Server
<br><br>基本使用:介绍`@EnableConfigserver`、`Environment` 仓储、秘钥管理等基本使用方法
<br><br>分布式配置官方实现:介绍Spring官方标准分布式配置实现方式: Git实现和文件系统实现
<br><br>动态配置属性Bean :介绍`@RefreshScope`基本用法和使用场景,丢且说明其中的局限性。
<br><br>健康指标:介绍Spring Boot标准端口( `/health` )以及健康指标( Health Indicator)。
<br><br>分布式配置自定义实现:基于配置管理容器Zookeeper ,自定义实现分布式配置能力。
<br><br>健康指标自定义实现:实现分布式配置的健康指标自定义实现
#### Spring Cloud Netflix Eureka
<br><br>前微服务时代:介绍前微服务时代，服务发现和注册在SOA甚至是更早的时代的技术实现和实施方法,如WebService中的UDDI、REST 中的HEATOAS
<br><br>高可用架构:简介高可用架构的基本原则,计算方法和系统设计
<br><br>Eureka客户端:介绍Spring Cloud Discovery结合Netflix Eureka客户端的基本使用方法，包括服务发现激活、Eureka客户端注册配置以及API使用等
<br><br>Eureka服务器:介绍Eureka服务器作为服务注册中心的搭建方法,以及内建Dashboard基本运维手段
#### Spring Cloud Netfix Ribbon
<br><br>简介负载均衡客户端和服务端的相关理论,包括调度算法:如先来先服务、轮训、多级队列等。基本特性:非对称负载、健康检查、优先级队列等
<br><br>技术回顾:回顾Spring Framework HTTP组件RestTemplate的使用方法,结台ClientHttpRequestInterceptor实现简单负载均衡客户端
<br><br>整合Netlix Ribbon :作为Spring Cloud客户端负载均衡实现, Netflix Ribbon提供了丰富的组件,包括负载均衡器、负载均衡规则、PING 策略等,根据前章所积累的经验,实现客户端负载均衡
####  Spring Cloud Hystrix
<br><br>核心理念:介绍服务短路的名词由来、目的,以及相关的类似慨念。随后讲述其中设计哲学、触发条件、处理手段以及客户端和服务端实现方法
<br><br>Spring Cloud Hystrix :作为服务端服务短路实现,介绍Spring Cloud Hytrix常用限流的功能,同时,说明健康指标以及数据指标在生产环境下的现实意义
<br><br>生产准备特性:介绍聚台数据指标Turbine、Turbine Stream ,以及整台Hystrix Dashboard


