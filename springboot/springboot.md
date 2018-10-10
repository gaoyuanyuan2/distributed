# Spring Boot
<br><br>![持久化存储](https://github.com/gaoyuanyuan2/distributed/blob/master/img/49.jpg) 
<br>主要内容
<br>Spring Boot技术栈:介绍Spring Boot完整的技术栈,比如Web应用、数据操作、消息、测试以及运维管理等
<br>Spring Boot构建方式:介绍图形化以及命令行方式构建Spring Boot项目Spring Boot多模块应用:构建分层、多模块Spring Boot应用
<br>Spring Boot运行方式:分别介绍IDEA启动、命令行启动以Maven插件启动方式
<br>Spring Boot简单应用:使用Spring Web MVC以及Spring Web Flux技术,编程简单应用。理解Spring Boot三大特性:自动装配、嵌入式容器、为生产准备的特性
<br><br>三种嵌入式容器 实现通用接口 不同环境通过不同classpath读取不同的类 spring自动装配 的条件装配 如果有就加载 一直查找
## Spring Boot实际使用场景
<br>在Spring Boot 2.0.0，如果应用采用Spring Web MVC作为Web服务，默认情况下， 使用嵌入式Tomcat。
<br>如果采用Spring Web Flux，默认情况下，使用Netty WebServer (嵌入式)
## 特性
<br>1. JAVA8
<br><br>2.  Web flux
<br><br>1) 函数编程: Java 8 Lambda
<br><br>2) 响应编程: Reactive Streams
<br><br>3) 异步编程: Servlet 3.1或Asyc NIO(异步非阻塞提高吞吐量)
<br><br>3.  爬虫式编程
<br><br>4.  函数式断点，rest暴露
<br><br>5.  SpringBoot的三种启动方式:
<br><br>1) IDE启动
<br><br>2) 项目文件下，执行`mvn spring-boot:run`命令
<br><br>3) 项目文件下，执行`mvn install`，然后target目录下找到jar文件，执行`java -jar jar`文件
<br><br>6.  SpringBoot特点
<br><br>1) 基于spring ,使开发者快速入门,门槛很低。(Spring全家桶)
<br><br>2) SpringBoot可以创建独立运行的应用而不依赖于容器
<br><br>3) 不需要打包成war包,可以放入tomcat中直接运行
<br><br>4) 提供可视化的相关功能,方便监控,比如性能,应用的健康程度等
<br><br>5) 简化配置,不用再看过多的xml
<br><br>6) 为微服务SpringCloud铺路 , SpringBoot可以整合很多各式各样的
<br><br>7) 框架来构建微服务,比如dubbo , thrift等等
<br><br>8)快速创建独立运行的Spring项目以及与主流框架集成t使用嵌入式的Servlet容器,应用无需打成WAR包starters自动依赖与版本控制
<br><br>9)大量的自动配置,简化开发,也可修改默认值
<br><br>10)无需配置XML ,无代码生成,开箱即用准生产环境的运行时应用监控与云计算的天然集成
<br><br>7.  SpringBoot 是对spring 的封装 要了解spring底层api
## 打包
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```
## 热部署
<br>1.  devtools可以实现页面热部署(即页面修改后会立即生效，这个可以直接在application.properties文件中配置`spring.thymeleaf.cache`设为false来实现)
<br><br>2.  实现类文件热部署(类文件修改后不会立即生效)，实现对属性文件的热部署。即devtools会监听classpath下的文件变动，并且会立即重启应用(发生在保存时机) ,
`注意:因为其采用的虚拟机机制，该项重启是很快的`
<br><br>1) base classloader (Base类加载器) ;加载不改变的Class. 例如:第三方提供的jar包。
<br><br>2) restart classloader (Restart类加载器) :加载正在开发的Class
<br><br>3.  为什么重启很快，因为重启的时候只是加载了在开发的Class,没有重新加载第三方的jar包
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <!-- optional=true,依赖不会传递，该项目依赖devtools;之后依赖boot项目的项目如果想要使用devtools,需要重新引入-->
    <optional>true</optional>
</dependency>
```
```properties
#关闭缓存，即时刷新
spring.thymeleaf.cache=true
#热部署生效
spring.devtools.restart.enabled=true
#设置重启的目录，添加那个目录的文件需要restart
spring.devtools.restart.additional-paths=src/main/java
#排除那个目录的文件不需要restart
#spring.devtools.restart.exclude=static/**, public/**
#classpath目录下的WEB- INF文件夹内容修改不重启
#spring.devtools.restart.exclude=WEB- INF/**
```
## 日志
<br>1. SpringBoot :底层是Spring框架, Spring框架默认是用JCL 
<br>SpringBoot选用SLF4j和(日志门面)logback(日志实现) ;
<br><br>2. 日志的级别
<br>由低到高  trace<debug<info<warn<error
## 自动装配
<br>`@SpringBootConfiguration:` SpringBoot的配置类;标注在某个类上,表示这是一个Spring Boot的配置类;
<br>`@Configuration`: 配置类上来标注这个注解; 配置类----配置文件;配置类也是容器中的一个组件; `@Component`
<br>`@EnableAutoConfiguration` 开启自动配置功能;以前我们需要配置的东西, Spring Boot帮我们自动配置; `@EnableAutoConfiguration`告诉SpringBoot开启自动配置功能;这样自动配置才能生效;
```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {...}
```
`@AutoConfigurationPackage` :自动配置包
<br>`@Import(AutoConfigurationPackages.Registrar.class)` : Spring的底层注解`@Import` ,给容器中导入一个组件;
<br>导入的组件由`AutoConfigurationPackages.Registrar.class` ;
<br>将主配置类( `@SpringBootApplication`标注的类)的所在包及下面所有子包里面的所有组件扫描到Spring容器;
<br>`@Import(EnableAutoConfigurationImportSelector.class)`:给容器中导入组件?
<br>`EnableAutoConfigurationImportSelector` :导入哪些组件的选择器;
<br>将所有需要导入的组件以全类名的方式返回;这些组件就会被添加到容器中;
<br>会给容器中导入非常多的自动配置类(`xxxAutoConfiguration`) ; 就是给容器中导入这个场景需要的所有组件,并配置好这些组件;
```java
public abstract class SpringFactoriesLoader {
	/**
	 * The location to look for factories.
	 * <p>Can be present in multiple JAR files.
	 */
	public static final String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
	...
}
```
```properties
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
```
<br>有了自动配置类,免去了我们手动编写配置注入功能组件等的工作;
<br>`SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class,classloader)` ;
<br>Spring Boot在启动的时候从类路径下的`ETA-INF/spring.factories`中获取`EnableAutoConfiguration`指定的值，将这些值作为自动配置类导，入到容器中，自动配置类就生效，帮我们进行自动配置工作;
<br>以前我们需要自己配置的东西，自动配置类都帮我们;
<br>J2EE的整体整合解决方案和自动配置都在`spring-boot-autoconfigure-x.x.x.RELEASE.jar` ;
## 配置
<br>导入配置文件处理器，配置文件进行绑定就会有提示
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-configuration-processor</artifactId>
	<optional>true</optional>
</dependency>
```
`@Value`获取值和`@ConfigurationProperties`获取值比较

|            | @ConfigurationProperties | @Value |
| ---------- | ------------------------ | ------ |
| 功能 | 批量注入配置文件中的属性 | 一个个指定  |
| 松散绑定（松散语法）| 支持 | 不支持|
| SpEL | 不支持 | 支持|
| JSR303数据校验 | 支持| 不支持 |
| 复杂类型封装 | 支持 | 不支持  |

<br><br>配置文件yml还是properties他们都能获取到值；
<br>如果说，我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用`@Value`；
<br>如果说，我们专门编写了一个javaBean来和配置文件进行映射，我们就直接使用`@ConfigurationProperties`；
## Spring Web MVC
### 主要内容
<br>Spring Web MVC介绍:整体介绍Spring Web MVC框架设计思想、功能特性、以及插播式实现
<br>Spring Web MVC实战:详细说明DispatcherServlet、`@Controller` 和`@RequestMapping`的基本原理、`@RequestParam` 、`@RequestBody` 和`@ResponseBody`使用方式、以及它们之间关系
<br>映射处理:介绍DispatcherServlet与RequestMappingHandlerMapping之间的交互原理，HandlerInterceptor 的职责以及使用
<br>异常处理:介绍DispatcherServlet中执行过程中,如何优雅并且高效地处理异常的逻辑,如归类处理以及提供友好的交互界面等Thymeleaf
<br>视图技术:介绍新-代视图技术Thymeleaf ,包括其使用场景、实际应用以及技术优势
<br>视图解析:介绍Spring Web MVC视图解析的过程和原理、以及内容协调视图处理器的使用场景
<br>国际化:利用Locale技术,实现视图内容的国际化
### MVC
<br>M : Model
<br>V : View
<br>C : Controller -> DispatcherServlet
<br>Front Controller = DispatcherServlet
<br>Application Controller = `@Controller` or Controller
<br>ServletContextListener -> ContextLoaderListener -> Root WebApplicationContext
<br>DispatcherServlet -> Servlet WebApplicationContext
<br>Services => `@Service`
<br>Repositories => `@Repository`											  
###  请求映射
<br>Servlet / 和 /*
<br>ServletContext path = /servlet-demo
<br>URI : `/servlet-demo/IndexServlet`
<br>DispatcherServlet < FrameworkServlet < HttpServletBean < HttpServlet
<br>自动装配 : `org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration`
<br>ServletContext path = "" or "/"
<br>Request URI = ServletContext path + @RequestMapping("")/ @GetMapping()
<br>当前例子：
<br>Request URI = "" + "" = "" -> RestDemoController#index()
<br>Request URI : "" 或者 "/"
<br>HandlerMapping ，寻找Request URI，匹配的 Handler ：
	<br>Handler：处理的方法，当然这是一种实例
	<br>整体流程：Request -> Handler -> 执行结果 -> 返回（REST） -> 普通的文本	
	<br>请求处理映射：RequestMappingHandlerMapping -> @RequestMapping Handler Mapping	
	<br>拦截器：HandlerInterceptor 可以理解 Handler 到底是什么
			<br>处理顺序：preHandle(true) -> Handler: HandlerMethod 执行(Method#invoke) -> postHandle -> afterCompletion
					  preHandle(false)
<br><br>Spring Web MVC 的配置 Bean ：WebMvcProperties
<br>Spring Boot 允许通过 application.properties 去定义一下配置，配置外部化
<br>WebMvcProperties 配置前缀：spring.mvc
<br>spring.mvc.servlet 
### 异常处理
<br>1. 传统的Servlet web.xml 错误页面
* 优点：统一处理，业界标准
* 不足：灵活度不够，只能定义 web.xml文件里面
<br><error-page> 处理逻辑：
 * 处理状态码 <error-code>
 * 处理异常类型 <exception-type>
 * 处理服务：<location>
<br><br>2. Spring Web MVC 异常处理
 * @ExceptionHandler
    * 优点：易于理解，尤其是全局异常处理
    * 不足：很难完全掌握所有的异常类型
 * @RestControllerAdvice = @ControllerAdvice + @ResponseBody
 * @ControllerAdvice 专门拦截（AOP） @Controller
<br><br>3.  Spring Boot 错误处理页面
 * 实现 ErrorPageRegistrar
    * 状态码：比较通用，不需要理解Spring WebMVC 异常体系
    * 不足：页面处理的路径必须固定
 * 注册 ErrorPage 对象
 * 实现 ErrorPage 对象中的Path 路径Web服务
### 视图技术
<br>View
<br><br>1. render 方法
<br>处理页面渲染的逻辑，例如：Velocity、JSP、Thymeleaf
<br><br>2. ViewResolver
<br>View Resolver = 页面 + 解析器 -> resolveViewName 寻找合适/对应 View 对象
<br>RequestURI-> RequestMappingHandlerMapping ->
<br>HandleMethod -> return "viewName" ->
<br>完整的页面名称 = prefix + "viewName" + suffix 
-> ViewResolver -> View -> render -> HTML
<br>Spring Boot 解析完整的页面路径：
<br>spring.view.prefix + HandlerMethod return + spring.view.suffix
<br><br>3. ContentNegotiationViewResolver
<br>用于处理多个ViewResolver：JSP、Velocity、Thymeleaf
<br>当所有的ViewResolver 配置完成时，他们的order 默认值一样，所以先来先服务（List）
<br>当他们定义自己的order，通过order 来倒序排列
<br><br>4.  Thymeleaf
<br>自动装配类：ThymeleafAutoConfiguration
<br>配置项前缀：spring.thymeleaf
<br>模板寻找前缀：spring.thymeleaf.prefix
<br>模板寻找后缀：spring.thymeleaf.suffix
<br>代码示例：/thymeleaf/index.htm
<br>prefix: /thymeleaf/
<br>return value : index
<br>suffix: .htm
### 国际化（i18n）
<br>Locale
`LocaleContextHolder`
## Spring Boot REST
### 基本概念	
REST = RESTful = Representational State Transfer，is one way of providing interoperability between computer systems on the Internet.
 [参考资源](https://en.wikipedia.org/wiki/Representational_state_transfer)    
### 1、目标
<br>1.  理解“资源操作”（Manipulation of resources through representations）
<br>POST、GET、PUT、DELETE
<br>幂等方法
<br><br>2.  理解“自描述消息” （Self-descriptive messages）
<br>Content-Type
<br>MIME-Type
<br><br>3.  扩展“自描述消息”
<br>HttpMessageConvertor
### 2、理解幂等
<br>1.  PUT 幂等
<br>初始状态：0
<br>修改状态：1 * N
<br>最终状态：1
<br><br>2.  DELETE 幂等
<br>初始状态：1
<br>修改状态：0 * N
<br>最终状态：0
<br><br>3.  POST 非幂等
<br>初始状态：1
<br>修改状态：1 + 1 =2
<br>N次修改： 1+ N = N+1
<br>最终状态：N+1
<br><br>幂等/非幂等 依赖于服务端实现，这种方式是一种契约
### 3、自描述消息
<br>1.  请求头
`Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8`
<br>第一优先顺序：`text/html` -> `application/xhtml+xml` -> `application/xml`
<br>第二优先顺序：`image/webp` -> `image/apng`
<br><br>2.  自描述消息处理器
<br>所有的 HTTP 自描述消息处理器均在 messageConverters（类型：`HttpMessageConverter`)，这个集合会传递到 RequestMappingHandlerAdapter，最终控制写出。
<br>messageConverters，其中包含很多自描述消息类型的处理，比如 JSON、XML、TEXT等等
<br>以 application/json 为例，Spring Boot 中默认使用 Jackson2 序列化方式，其中媒体类型：application/json，它的处理类 MappingJackson2HttpMessageConverter，提供两类方法：
<br><br>1) 读read* ：通过 HTTP 请求内容转化成对应的 Bean
<br><br>2) 写write*： 通过 Bean 序列化成对应文本内容作为响应内容
### 4、扩展自描述消息
<br>1.  实现 AbstractHttpMessageConverter 抽象类
<br><br>1)  supports 方法：是否支持当前POJO类型
<br><br>2)  readInternal 方法：读取 HTTP 请求中的内容，并且转化成相应的POJO对象（通过 Properties 内容转化成 JSON）
<br><br>3)  writeInternal 方法：将 POJO 的内容序列化成文本内容（Properties格式），最终输出到 HTTP 响应中（通过 JSON 内容转化成 Properties ）  
<br><br>2.  HttpMessageConverter 执行逻辑：
<br>读操作：尝试是否能读取，canRead 方法去尝试，如果返回 true 下一步执行 read
<br>写操作：尝试是否能写入，canWrite 方法去尝试，如果返回 true 下一步执行 write
<br><br>3. 编码
<br><br>1.  Properties 格式（application/properties+person)
<br>需要扩展
```properties
person.id = 1
person.name = yan
```
<br><br>2. json -> properties
<br>请求地址：`/person/json/to/properties`
<br>请求头
```properties
Accept=application/properties+person
Content-Type=application/json
```
<br>请求体
```json
{
    "id":7,
    "name":"yan"
}
```
<br>响应体
```properties
#Written by web server
#Tue Sep 18 15:35:00 CST 2018
person.name=yan
person.id=7
```
<br><br>3.properties -> json
<br>请求地址：`/person/properties/to/json`
<br>请求头
```properties
Content-Type=application/properties+person
Accept=application/json
```
<br>请求体
```properties
person.name=yan
person.id=7
```
<br>响应体
```json
{
    "id":7,
    "name":"yan"
}
```
<br><br>3. java 代码
<br><br>1)  配置
```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    public void extendMessageConverters(
            List<HttpMessageConverter<?>> converters) {

        converters.add(new PropertiesPersonHttpMessageConverter());
    }

}
```
<br><br>2)  controller
```java
    @PostMapping(value = "/person/json/to/properties", produces = "application/properties+person")
    public Person personJsonToProperties(@RequestBody Person person) {
        // @RequestBody 的内容是 JSON
        // 响应的内容是 Properties
        return person;
    }

    @PostMapping(value = "/person/properties/to/json", consumes = "application/properties+person", produces = 
            MediaType.APPLICATION_JSON_UTF8_VALUE)
    public Person personPropertiesToJson(@RequestBody Person person) {
        // @RequestBody 的内容是 Properties
        // 响应的内容是 JSON
        return person;
    }
```
<br><br>3) 自定义HttpMessageConverter
```java
public class PropertiesPersonHttpMessageConverter extends AbstractHttpMessageConverter<Person> {

    public PropertiesPersonHttpMessageConverter() {
        super(MediaType.valueOf("application/properties+person"));
        setDefaultCharset(Charset.forName("UTF-8"));
    }

    @Override
    protected boolean supports(Class<?> clazz) {
        return clazz.isAssignableFrom(Person.class);
    }
    /**
     * 讲请求内容中 Properties 内容转化成 Person 对象
     * @throws IOException
     * @throws HttpMessageNotReadableException
     */
    @Override
    protected Person readInternal(Class<? extends Person> clazz, HttpInputMessage inputMessage) throws IOException, 
            HttpMessageNotReadableException {

        /**
         * person.id = 1
         * person.name = yan
         */
        InputStream inputStream = inputMessage.getBody();
        Properties properties = new Properties();
        // 将请求中的内容转化成Properties
        properties.load(new InputStreamReader(inputStream, getDefaultCharset()));
        // 将properties 内容转化到 Person 对象字段中
        Person person = new Person();
        person.setId(Long.valueOf(properties.getProperty("person.id")));
        person.setName(properties.getProperty("person.name"));
        return person;
    }

    @Override
    protected void writeInternal(Person person, HttpOutputMessage outputMessage) throws IOException, 
            HttpMessageNotWritableException {
        OutputStream outputStream = outputMessage.getBody();
        Properties properties = new Properties();
        properties.setProperty("person.id", String.valueOf(person.getId()));
        properties.setProperty("person.name", person.getName());
        properties.store(new OutputStreamWriter(outputStream, getDefaultCharset()), "Written by web server");
    }
}
```
### 5. 注意
`@RequestMappng` 中的 consumes 对应 请求头 `Content-Type`

`@RequestMappng` 中的 produces 对应 请求头 `Accept`
### 6、思考
<br>1.  当 Accept 请求头未被制定时，为什么还是 JSON 来处理？
<br>查看源码：
<br>DelegatingWebMvcConfiguration的父类WebMvcConfigurationSupport->addDefaultHttpMessageConverters方法，可以看出messageConverters插入顺序。
从插入顺序可以看出Spring Boot 中默认使用 Jackson2 序列化方式，采用轮训的方式去逐一尝试是否可以 HttpMessageConverter<T>->canWrite(POJO) ,如果返回 true，说明可以序列化该
 POJO 对象。
<br><br>2.  优先级是默认的是可以修改吗？
<br>是可以调整的，通过extendMessageConverters 方法调整
<br>同一个WebMvcConfigurerAdapter中的configureMessageConverters方法先于extendMessageConverters方法执行
<br>可以理解为是三种方式中最后执行的一种，不过这里可以通过add指定顺序来调整优先级，也可以使用remove/clear来删除converter
<br>使用converters.add(xxx)会放在最低优先级（List的尾部）
<br>使用converters.add(0,xxx)会放在最高优先级（List的头部）
<br><br>3.  为什么第一次是JSON，后来怎加了 XML 依赖，又变成了 XML 内用输出
<br>Spring Boot 应用默认没有增加XML 处理器（HttpMessageConverter）实现，所以最后采用轮训的方式去逐一尝试是否可以 canWrite(POJO) ,如果返回 true，说明可以序列化该 POJO 对象，那么 Jackson 2 恰好能处理，那么Jackson 输出了。
## 数据库JDBC
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
<br>TRANSACTION_READ_UNCOMMITTED
<br>TRANSACTION_READ_COMMITTED
<br>TRANSACTION_REPEATABLE_READ
<br>TRANSACTION_SERIALIZABLE
<br><br>保护点( SavePoints)
<br><br>从上至下,级别越高,性能越差
<br>Spring Transaction实现重用了JDBC API :
<br>Isolation -> TransactionDefinition
<br>ISOLATION_READ_UNCOMMITTED = Connection.TRANSACTION_ READ_UNCOMMITTED
<br>ISOLATION_READ_COMMITTED = Connection.TRANSACTION_READ_COMMITTED
<br>ISOLATION_REPEATABLE_READ = Connection.TRANSACTION_REPEATABLE_READ
<br>ISOLATION_SERIALIZABLE = Connection.TRANSACTION_SERIALIZABLE
<br><br>@Transaction
<br>代理执行 TransactionInterceptor
<br>可以控制rollback的异常粒度: rollbackFor() 以及noRollbackFor()
<br>可以执行事务管理器: transactionManager()
<br><br>通过API方式进行事务处理- PlatformTransactionManager
## 验证
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
## Spring Web Flux
<br>如果采用Spring Web Flux，默认情况下，使用Netty WebServer (嵌入式)
<br>从Spring Boot 1.4支持FailureAnalysisReporter实现
<br><br>WebFlux
<br>Mono:  0-1 Publisher ( 类似于Java 8中的Optional)
<br>Flux:  0-N Publisher (类似于ava中的List)
<br>传统的Servlet采用`HttpServletRequest.HttpServletResponse`
<br>WebFlux采用: ServerRequest、 ServerResponse (不再限制于Servlet容器，可以选择自定义实现，  比如Netty Web Server )
## 事件 
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
<br>`java.util.Observer`是订阅者
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
### 问题集合
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
<br><br>7. spring的老项目迁移到SpringBoot,怎么弄
<br>答:老的XML方式采用 `@ImportResource`导入!
<br><br>8.  嵌入式tomcat如何调优
<br>答:第一种通过application.properties文件调整配置参数
<br>第二种通过接口回调:
<br>TomcatConnectorCustomizer
<br>TomcatContextCustomizer