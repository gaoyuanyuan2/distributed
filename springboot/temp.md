## SpringBoot
事务( Transaction )
Spring Boot场景演示
Annotation驱动

API驱动

事务(Transaction )
事务
事务用于提供数据完整性,并在并发访问下确保数据视图的一致性。

重要概念
自动提交模式( Auto-commit mode )
事务隔离级别( Transaction isolation levels )
保护点( Savepoints )

@ Transaction
代理执行- TransactionInterceptor
可以控制rollback的异常粒度: rollbackFor() 以及noRollbackFor()
可以执行事务管理器: transactionManager()

线程1:
调用save ->
@Transactional T1 save() 控制DS 1 insert ->

save2() DS 1 insert
@Transactional
save() {
//insert DS1
save1() // insert DS2,没有Transactional

1.用reactive web，原来mvc的好多东西都不能用了?
答:不是，
Reactive Web还是能够兼容Spring.WebMVC

2.开个线程池事务控制用API方式?比如开始写的Excutor.fixExcutor(5)

答: TransactionSynchronizationManager使用大量的ThreadLocal来实现的

I
3.假设一- 个service方法给了@Transaction标签，在这个方法中还有其他service的某个方法，这个方法没有加@Transaction，那么如果内部方法报错，会回滚吗?

答:会的，当然可以过滤掉一- 些不关紧要的异常noRollbackFor()


第一节Spring Boot初体验
主要内容
。Spring Boot技术栈:介绍Spring Boot完整的技术栈,比如Web应用、数据操作、消息、测试以及运维管理等
Spring Boot构建方式:介绍图形化以及命令行方式构建Spring Boot项目Spring Boot多模块应用:构建分层、多模块Spring Boot应用
。Spring Boot运行方式:分别介绍IDEA启动、命令行启动以Maven插件启动方式
。Spring Boot简单应用:使用Spring Web MVC以及Spring Web Flux技术,编程简单应用。理解Spring Boot三大特性:自动装配、嵌入式容器、为生产准备的特性

第四节数据库JDBC
主要内容
数据源( DataSource) : 分别介绍嵌入式数据源、通用型数据源以及分布式数据源。事务( Transaction) : 介绍事务原理,本地事务和分布式事务的使用场景
0 JDBC( JSR-221 ) :介绍JDBC核心接口,数据源、数据库连接、执行语句、事务等核心API的使用方法

C第五节验证
主要内容
。Bean Validation ( JSR-303 ) : 介绍Java Bean验证、核心API、实现框架Hibernate Validator。Apache commons-validator :介绍最传统Apache通用验证器框架,如:长度、邮件等方式。Spring Validator :介绍Spring内置验证器API、 以及自定义实现

Java 9里面API称之为Flow (流)
Publisher -> publish(1)
Subscription (1) :订阅消息
Subs(A)#onNext() -> Subs(B)#onNext() -> Subs(C)#onNext()

Reactive是推模式( Push )
Iterator是拉模式(Pull)


问: webFluxConfiguration 里面的映射路径和controller里面的路径有什么区别吗

答:基本上是没有区别的，注意，不重复定义，或者URL语义有重复!

问: webFlux不是跟mvc不能一起吗， 怎么一起启动了

答:  spring-boot-starter-webmvs和spring-boot-starter-webflux 可以放在同一个应用，可是webflux不会工作，默认使用webmvc, webflux不会被采用。其实webflux是兼容Annotation驱动，比如

      @RequestMapping

问: webFlux可以定义restFull吗
答:  可以的，支持的!

问: spring的老项目迁移到springboot,怎么弄
答:老的XML方式采用| @ImportResource导入!

问:  嵌入式tomcat如何调优
答:第一种通过application.properties文件调整配置参数
第二种通过接口回调:
TomcatConnectorCustomizer
TomcatContextCustomizer


第二节Spring Web MVC

      主要内容

      。Spring Web MVC介绍:整体介绍Spring Web MVC框架设计思想、功能特性、以及插播式实现

      Spring Web MVC实战:详细说明DispatcherServlet、@Controller 和@RequestMapping的基本原理、@RequestParam 、@RequestBody 和@ResponseBody使用方式、以及它们之间关系

      映射处理:介绍DispatcherServlet与RequestMappingHandlerMapping之间的交互原理，HandlerInterceptor 的职责以及使用异常处理:介绍DispatcherServlet中执行过程中,如何优雅并且高效地处理异常的逻辑,如归类处理以及提供友好的交互界面等Thymeleaf视图技术:介绍新-代视图技术Thymeleaf ,包括其使用场景、实际应用以及技术优势视图解析:介绍Spring Web MVC视图解析的过程和原理、以及内容协调视图处理器的使用场景国际化:利用Locale技术,实现视图内容的国际化

第三节REST

      主要内容

      。REST 理论基础:基本概念、架构属性、架构约束、使用场景、实现框架(服务端、客户端)

      REST服务端实践: Spring Boot REST应用、HATEOAS 应用、文档生成等

      REST客户端实践:传统浏览器、Apache HttpClient、Spring RestTemplate等相关实践

第四节数据库JDBC

      主要内容

      数据源( DataSource) : 分别介绍嵌入式数据源、通用型数据源以及分布式数据源务(Transaction) : 介绍事务原理,本地事务和分布式事务的使用场景

      JDBC ( JSR-221) : 介绍JDBC核心接口,数据源、数据库连接、执行语句、事务 等核心API的使用方法

@第五节睑证I

      主要内容

      。Bean Validation ( JSR-303) : 介绍Java Bean验证、核心API、实现框架Hibernate Validator

      Apache commons-alidator :介绍最传统Apache通用验证器框架,如:长度、邮件等方式Spring Validator :介绍Spring内置验证器API、以及自定义实现

Spring Assert API
JVM/Java assert断言

以上方式的缺点，耦合了业务逻辑，虽然可以通过HandlerInterceptor或者Filter做拦截，但是也是非常恶心的
还可以通过AOP的方式，也可以提升代码的可读性。

以上方法都有一个问题，不是统-一的标准。


1. JSON校验如何办?
答:尝试变成Bean的方式
2.实际中很多参数都要校验那时候怎么写这样写会增加很多类
答:  确实会增加部分工作量，大多数场景，不需要自定义，除非很特殊情况。Bean Validation的主要缺点，单元测试不方便

Spring / Spring Boot事件机制
设计模式
观察者模式
事件/监听器模式Spring核心接口一ApplicationEvent
ApplicationListener

