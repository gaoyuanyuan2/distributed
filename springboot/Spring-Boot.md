#### Spring Boot初体验
<br><br>![持久化存储](https://github.com/gaoyuanyuan2/distributed/blob/master/img/49.jpg) 
<br>主要内容
<br>Spring Boot技术栈:介绍Spring Boot完整的技术栈,比如Web应用、数据操作、消息、测试以及运维管理等
<br>Spring Boot构建方式:介绍图形化以及命令行方式构建Spring Boot项目Spring Boot多模块应用:构建分层、多模块Spring Boot应用
<br>Spring Boot运行方式:分别介绍IDEA启动、命令行启动以Maven插件启动方式
<br>Spring Boot简单应用:使用Spring Web MVC以及Spring Web Flux技术,编程简单应用。理解Spring Boot三大特性:自动装配、嵌入式容器、为生产准备的特性

#### Spring Boot实际使用场景
<br>在Spring Boot 2.0.0，如果应用采用Spring Web MVC作为Web服务，默认情况下， 使用嵌入式Tomcat。
<br>如果采用Spring Web Flux，默认情况下，使用Netty WebServer (嵌入式)

#### 数据库JDBC
<br>主要内容
<br>数据源( DataSource) : 分别介绍嵌入式数据源、通用型数据源以及分布式数据源务(Transaction) : 介绍事务原理,本地事务和分布式事务的使用场景
<br>JDBC ( JSR-221) : 介绍JDBC核心接口,数据源、数据库连接、执行语句、事务 等核心API的使用方法
<br><br>数据连接池技术
<br>[Apache Commons DBCP]( http://commons.apache.org/proper/commons-dbcp/)
<br><br>事务( Transaction )
<br>事务用于提供数据完整性,并在并发访问下确保数据视图的一致性。
<br><br>重要概念
<br>自动提交模式( Auto-commitmode )
<br>事务隔离级别( Transaction isolation levels )
<br>保护点( Savepoints )

<br>@Transaction
<br>代理执行- TransactionInterceptor
<br>可以控制rollback的异常粒度: rollbackFor() 以及noRollbackFor()
<br>可以执行事务管理器: transactionManager()
#### 验证
<br>主要内容
<br>ean Validation ( JSR-303) : 介绍Java Bean验证、核心API、实现框架Hibernate Validator
<br>Apache commons-alidator :介绍最传统Apache通用验证器框架,如:长度、邮件等方式Spring Validator :介绍Spring内置验证器API、以及自定义实现
<br><br>Spring Assert API
<br>JVM/Java assert断言
<br><br>以上方式的缺点，耦合了业务逻辑，虽然可以通过HandlerInterceptor或者Filter做拦截，但是也是非常恶心的
<br>还可以通过AOP的方式，也可以提升代码的可读性。
<br>以上方法都有一个问题，不是统一的标准。
<br><br>1. JSON校验如何办?
<br>答:尝试变成Bean的方式
<br><br>2.实际中很多参数都要校验那时候怎么写这样写会增加很多类
<br>答:  确实会增加部分工作量，大多数场景，不需要自定义，除非很特殊情况。Bean Validation的主要缺点，单元测试不方便
#### Spring Web Flux
<br>如果采用Spring Web Flux，默认情况下，使用Netty WebServer (嵌入式)
<br>从Spring Boot 1.4支持FailureAnalysisReporter实现
<br><br>WebFlux
<br>Mono:  0-1 Publisher ( 类似于Java 8中的Optional)
<br>Flux:  0-N Publisher (类似于ava中的List)
<br>传统的Servlet采用`HttpServletRequest.HttpServletResponse`
<br>WebFlux采用: ServerRequest、 ServerResponse (不再限制于Servlet容器，可以选择自定义实现，  比如Netty Web Server )
#### 事件 
<br>Java9里面API称之为Flow(流)
<br>Publisher -> publish(1)
<br>Subscription 
<br><br>(1) :订阅消息
<br>Subs(A)#onNext() -> Subs(B)#onNext() -> Subs(C)#onNext()
<br>Reactive是推模式(Push)
<br>Iterator是拉模式(Pull)
<br><br>Spring / Spring Boot事件机制
<br>设计模式
<br>观察者模式
<br>事件/监听器模式Spring核心接口一ApplicationEvent
<br>ApplicationListener
<br><br>Spring Cloud Config Client
<br>`java.util.Observable`是一个发布者
<br>`java.util.0bserver`是订阅者
<br><br>发布者和订阅者:1:N
<br>发布者和订阅者:N:M
<br><br>事件/监听模式
<br>`java.util.Eventobject` :
<br><br>事件对象总是关联着事件源(source)
<br>`java.util.EventListener` :  事件监听接口(标记)
<br><br>Spring / Spring Boot事件机制
<br>Spring Boot核心事件
<br>一ApplicationEnvironmentPreparedEvent
<br>一ApplicationPreparedEvent
<br>一ApplicationStartingEvent
<br>一ApplicationReadyEvent
<br>一ApplicationFailedEvent
<br><br>Spring事件/监听
<br>ApplicationEvent :应用事件
<br>ApplicationListener:应用监听器
<br><br>Spring Boot事件/监听器
<br>ConfigFileApplicationListener
<br>管理配置文件，比如:  `application.properties`  以及`application.yml`
<br><br>Java SPI: `java.util.ServiceLoader`
<br>Spring SPI
<br><br>如何控制顺序
<br>实现Ordered 以及标记@Order
<br>在Spring里面，数值越小，越优先
<br><br>Spring Cloud事件/监听器
<br>BootstrapApplicationListener
<br>负责加载`bootstrap.properties`或者`bootstrap.yml`
#### 问题集合
<br>1.  用reactive web，原来mvc的好多东西都不能用了?
<br>答:不是，
<br>Reactive Web还是能够兼容Spring.WebMVC
<br><br>2.  开个线程池事务控制用API方式?比如开始写的`Excutor.fixExcutor(5)`
<br>答: TransactionSynchronizationManager使用大量的ThreadLocal来实现的
<br><br>3.  假设一个service方法给了@Transaction标签，在这个方法中还有其他service的某个方法，这个方法没有加@Transaction，那么如果内部方法报错，会回滚吗?
<br>答:会的，当然可以过滤掉一些不关紧要的异常noRollbackFor()
<br><br>4.  webFluxConfiguration 里面的映射路径和controller里面的路径有什么区别吗
<br>答:基本上是没有区别的，注意，不重复定义，或者URL语义有重复!
<br><br>5. webFlux不是跟mvc不能一起吗， 怎么一起启动了
<br>答:  spring-boot-starter-webmvc和spring-boot-starter-webflux 可以放在同一个应用，可是webflux不会工作，默认使用webmvc, 
webflux不会被采用。其实webflux是兼容Annotation驱动，比如
` @RequestMapping`
<br><br>6. webFlux可以定义restFull吗
<br>答:  可以的，支持的!
<br><br>7. spring的老项目迁移到springboot,怎么弄
<br>答:老的XML方式采用 @ImportResource导入!
<br><br>8.  嵌入式tomcat如何调优
<br>答:第一种通过application.properties文件调整配置参数
<br>第二种通过接口回调:
<br>TomcatConnectorCustomizer
<br>TomcatContextCustomizer