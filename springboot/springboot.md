# Spring Boot

![持久化存储](https://github.com/gaoyuanyuan2/distributed/blob/master/img/49.jpg) 

主要内容

Spring Boot技术栈:介绍Spring Boot完整的技术栈,比如Web应用、数据操作、消息、测试以及运维管理等

Spring Boot构建方式:介绍图形化以及命令行方式构建Spring Boot项目Spring Boot多模块应用:构建分层、多模块Spring Boot应用

Spring Boot运行方式:分别介绍IDEA启动、命令行启动以Maven插件启动方式

Spring Boot简单应用:使用Spring Web MVC以及Spring Web Flux技术,编程简单应用。

理解Spring Boot三大特性:自动装配、嵌入式容器、为生产准备的特性

三种嵌入式容器 实现通用接口 不同环境通过不同classpath读取不同的类 spring自动装配 的条件装配 如果有就加载 一直查找

## 更多SpringBoot整合示例

https://github.com/spring-projects/spring-boot/tree/master/spring-boot-samples

## Spring Boot实际使用场景

在Spring Boot 2.0.0，如果应用采用Spring Web MVC作为Web服务，默认情况下， 使用嵌入式Tomcat。

如果采用Spring Web Flux，默认情况下，使用Netty WebServer (嵌入式)

## 特性

1. JAVA8

2.  Web flux

1) 函数编程: Java 8 Lambda

2) 响应编程: Reactive Streams

3) 异步编程: Servlet 3.1或Asyc NIO(异步非阻塞提高吞吐量)

3.  爬虫式编程

4.  函数式断点，rest暴露

5.  SpringBoot的三种启动方式:

1) IDE启动

2) 项目文件下，执行`mvn spring-boot:run`命令

3) 项目文件下，执行`mvn install`，然后target目录下找到jar文件，执行`java -jar jar`文件

6.  SpringBoot特点

1) 基于spring ,使开发者快速入门,门槛很低。(Spring全家桶)

2) SpringBoot可以创建独立运行的应用而不依赖于容器

3) 不需要打包成war包,可以放入tomcat中直接运行

4) 提供可视化的相关功能,方便监控,比如性能,应用的健康程度等

5) 简化配置,不用再看过多的xml

6) 为微服务SpringCloud铺路 , SpringBoot可以整合很多各式各样的

7) 框架来构建微服务,比如dubbo , thrift等等

8)快速创建独立运行的Spring项目以及与主流框架集成使用嵌入式的Servlet容器,应用无需打成WAR包starters自动依赖与版本控制

9)大量的自动配置,简化开发,也可修改默认值

10)无需配置XML ,无代码生成,开箱即用准生产环境的运行时应用监控与云计算的天然集成

7.  SpringBoot 是对spring 的封装 要了解spring底层api

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

1.  devtools可以实现页面热部署(即页面修改后会立即生效，这个可以直接在application.properties文件中配置`spring.thymeleaf.cache`设为false来实现)

2.  实现类文件热部署(类文件修改后不会立即生效)，实现对属性文件的热部署。即devtools会监听classpath下的文件变动，并且会立即重启应用(发生在保存时机) 

`注意:因为其采用的虚拟机机制，该项重启是很快的`

1) base classloader (Base类加载器) ;加载不改变的Class. 例如:第三方提供的jar包。

2) restart classloader (Restart类加载器) :加载正在开发的Class

3.  为什么重启很快，因为重启的时候只是加载了在开发的Class,没有重新加载第三方的jar包

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

1. SpringBoot :底层是Spring框架, Spring框架默认是用JCL 

SpringBoot选用SLF4j和(日志门面)logback(日志实现) ;

2. 日志的级别

由低到高  trace<debug<info<warn<error

## 自动装配

`@SpringBootConfiguration:` SpringBoot的配置类;标注在某个类上,表示这是一个Spring Boot的配置类;

`@Configuration`: 配置类上来标注这个注解;配置类也是容器中的一个组件; `@Component`

`@EnableAutoConfiguration` 开启自动配置功能;以前我们需要配置的东西, Spring Boot帮我们自动配置; 

`@EnableAutoConfiguration`告诉SpringBoot开启自动配置功能;这样自动配置才能生效;(`@SpringBootApplication`已经包含这个注解)

如果该类只使用了`@ConfigurationProperties`注解，然后该类没有在扫描路径下或者没有使用`@Component`等注解，导致无法被扫描为bean，
那么就必须在配置类上使用`@EnableConfigurationProperties`注解去指定这个类，这个时候就会让该类上的`@ConfigurationProperties`生效，
然后作为bean添加进spring容器中

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {...}

```

`@AutoConfigurationPackage` :自动配置包

`@Import(AutoConfigurationPackages.Registrar.class)` : Spring的底层注解`@Import` ,给容器中导入一个组件;

导入的组件由`AutoConfigurationPackages.Registrar.class` ;

将主配置类( `@SpringBootApplication`标注的类)的所在包及下面所有子包里面的所有组件扫描到Spring容器;

`@Import(EnableAutoConfigurationImportSelector.class)`:给容器中导入组件

`EnableAutoConfigurationImportSelector` :导入哪些组件的选择器;

将所有需要导入的组件以全类名的方式返回;这些组件就会被添加到容器中;

会给容器中导入非常多的自动配置类(`xxxAutoConfiguration`) ; 就是给容器中导入这个场景需要的所有组件,并配置好这些组件;

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
 
 有了自动配置类,免去了我们手动编写配置注入功能组件等的工作;

`SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class,classloader)` ;

Spring Boot在启动的时候从类路径下的`ETA-INF/spring.factories`中获取`EnableAutoConfiguration`指定的值，将这些值作为自动配置类导，入到容器中，自动配置类就生效，帮我们进行自动配置工作;

以前我们需要自己配置的东西，自动配置类都帮我们;

J2EE的整体整合解决方案和自动配置都在`spring-boot-autoconfigure-x.x.x.RELEASE.jar` ;

`xxxxProperties`:封装配置文件中相关属性；

`@Conditional`派生注解（Spring注解版原生的`@Conditional`作用）

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

| @Conditional扩展注解                | 作用（判断是否满足当前指定条件）               |
| ------------------------------- | ------------------------------ |
| @ConditionalOnJava              | 系统的java版本是否符合要求                |
| @ConditionalOnBean              | 容器中存在指定Bean；                   |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean；                  |
| @ConditionalOnExpression        | 满足SpEL表达式指定                    |
| @ConditionalOnClass             | 系统中有指定的类                       |
| @ConditionalOnMissingClass      | 系统中没有指定的类                      |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                 |
| @ConditionalOnWebApplication    | 当前是web环境                       |
| @ConditionalOnNotWebApplication | 当前不是web环境                      |
| @ConditionalOnJndi              | JNDI存在指定项                      |

```java
@Configuration   //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件
@EnableConfigurationProperties(HttpEncodingProperties.class)  //启动指定类的ConfigurationProperties功能；将配置文件中对应的值和HttpEncodingProperties绑定起来；并把HttpEncodingProperties加入到ioc容器中
@ConditionalOnWebApplication //Spring底层@Conditional注解（Spring注解版），根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效；    判断当前应用是否是web应用，如果是，当前配置类生效
@ConditionalOnClass(CharacterEncodingFilter.class)  //判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)  //判断配置文件中是否存在某个配置  spring.http.encoding.enabled；如果不存在，判断也是成立的
//即使我们配置文件中不配置spring.http.encoding.enabled=true，也是默认生效的；
public class HttpEncodingAutoConfiguration {
  
  	//他已经和SpringBoot的配置文件映射了
  	private final HttpEncodingProperties properties;
  
   //只有一个有参构造器的情况下，参数的值就会从容器中拿
  	public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
		this.properties = properties;
	}
  
    @Bean   //给容器中添加一个组件，这个组件的某些值需要从properties中获取
	@ConditionalOnMissingBean(CharacterEncodingFilter.class) //判断容器没有这个组件
	public CharacterEncodingFilter characterEncodingFilter() {
		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
		filter.setEncoding(this.properties.getCharset().name());
		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
		return filter;
	}
```
```java
@ConfigurationProperties(prefix = "spring.http.encoding")  //从配置文件中获取指定的值和bean的属性进行绑定
public class HttpEncodingProperties {
   public static final Charset DEFAULT_CHARSET = Charset.forName("UTF-8");
}
```
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

配置文件yml还是properties他们都能获取到值；

只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用`@Value`；

专门编写了一个javaBean来和配置文件进行映射，我们就直接使用`@ConfigurationProperties`；

## Spring Web MVC

### 主要内容

Spring Web MVC介绍:整体介绍Spring Web MVC框架设计思想、功能特性、以及插播式实现

Spring Web MVC实战:详细说明DispatcherServlet、`@Controller` 和`@RequestMapping`的基本原理、`@RequestParam` 、`@RequestBody` 和`@ResponseBody`使用方式、以及它们之间关系
<br>映射处理:介绍DispatcherServlet与RequestMappingHandlerMapping之间的交互原理，HandlerInterceptor 的职责以及使用

异常处理:介绍DispatcherServlet中执行过程中,如何优雅并且高效地处理异常的逻辑,如归类处理以及提供友好的交互界面等Thymeleaf

视图技术:介绍新-代视图技术Thymeleaf ,包括其使用场景、实际应用以及技术优势

视图解析:介绍Spring Web MVC视图解析的过程和原理、以及内容协调视图处理器的使用场景

国际化:利用Locale技术,实现视图内容的国际化

### MVC

M : Model

V : View

C : Controller -> DispatcherServlet

Front Controller = DispatcherServlet

Application Controller = `@Controller` or Controller

ServletContextListener -> ContextLoaderListener -> Root WebApplicationContext

DispatcherServlet -> Servlet WebApplicationContext

Services => `@Service`

Repositories => `@Repository`	
										  
###  请求映射

Servlet / 和 /*

ServletContext path = /servlet-demo

URI : `/servlet-demo/IndexServlet`

DispatcherServlet < FrameworkServlet < HttpServletBean < HttpServlet

自动装配 : `org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration`

ServletContext path = "" or "/"

Request URI = ServletContext path + @RequestMapping("")/ @GetMapping()

当前例子：

Request URI = "" + "" = "" -> RestDemoController#index()

Request URI : "" 或者 "/"

HandlerMapping ，寻找Request URI，匹配的 Handler ：
	
	Handler：处理的方法，当然这是一种实例
	
	整体流程：Request -> Handler -> 执行结果 -> 返回（REST） -> 普通的文本	
	
	请求处理映射：RequestMappingHandlerMapping -> @RequestMapping Handler Mapping	
	
	拦截器：HandlerInterceptor 可以理解 Handler 到底是什么
			
			处理顺序：preHandle(true) -> Handler: HandlerMethod 执行(Method#invoke) -> postHandle -> afterCompletion
					  preHandle(false)

Spring Web MVC 的配置 Bean ：WebMvcProperties

Spring Boot 允许通过 application.properties 去定义一下配置，配置外部化

WebMvcProperties 配置前缀：spring.mvc

spring.mvc.servlet 

### 异常处理

1. 传统的Servlet web.xml 错误页面

* 优点：统一处理，业界标准

* 不足：灵活度不够，只能定义 web.xml文件里面

<error-page> 处理逻辑：

 * 处理状态码 <error-code>
 
 * 处理异常类型 <exception-type>

 * 处理服务：<location>

2. Spring Web MVC 异常处理
 
 * @ExceptionHandler
    
    * 优点：易于理解，尤其是全局异常处理
    
    * 不足：很难完全掌握所有的异常类型
 
 * @RestControllerAdvice = @ControllerAdvice + @ResponseBody
 
 * @ControllerAdvice 专门拦截（AOP） @Controller

3.  Spring Boot 错误处理页面
 
 * 实现 ErrorPageRegistrar
   
    * 状态码：比较通用，不需要理解Spring WebMVC 异常体系
    
    * 不足：页面处理的路径必须固定
 
 * 注册 ErrorPage 对象
 
 * 实现 ErrorPage 对象中的Path 路径Web服务

### 视图技术

View

1. render 方法

处理页面渲染的逻辑，例如：Velocity、JSP、Thymeleaf

2. ViewResolver

View Resolver = 页面 + 解析器 -> resolveViewName 寻找合适/对应 View 对象

RequestURI-> RequestMappingHandlerMapping ->

HandleMethod -> return "viewName" ->

完整的页面名称 = prefix + "viewName" + suffix -> ViewResolver -> View -> render -> HTML

Spring Boot 解析完整的页面路径：

spring.view.prefix + HandlerMethod return + spring.view.suffix

3. ContentNegotiationViewResolver

用于处理多个ViewResolver：JSP、Velocity、Thymeleaf

当所有的ViewResolver 配置完成时，他们的order 默认值一样，所以先来先服务（List）

当他们定义自己的order，通过order 来倒序排列

4.  Thymeleaf

自动装配类：ThymeleafAutoConfiguration

配置项前缀：spring.thymeleaf

模板寻找前缀：spring.thymeleaf.prefix

模板寻找后缀：spring.thymeleaf.suffix

代码示例：/thymeleaf/index.htm

prefix: /thymeleaf/

return value : index

suffix: .htm

### 国际化（i18n）

Locale

`LocaleContextHolder`

## Spring Boot REST

### 基本概念	

REST = RESTful = Representational State Transfer，is one way of providing interoperability between computer systems on the Internet.

 [参考资源](https://en.wikipedia.org/wiki/Representational_state_transfer)    
 
### 1、目标

1.  理解“资源操作”（Manipulation of resources through representations）

POST、GET、PUT、DELETE

幂等方法

2.  理解“自描述消息” （Self-descriptive messages）

Content-Type

MIME-Type

3.  扩展“自描述消息”

HttpMessageConvertor

### 2、理解幂等(规范的设计)

1.  PUT 幂等

初始状态：0

修改状态：1 * N

最终状态：1

2.  DELETE 幂等

初始状态：1

修改状态：0 * N

最终状态：0

3.  POST 非幂等

初始状态：1

修改状态：1 + 1 =2

N次修改： 1+ N = N+1

最终状态：N+1

幂等/非幂等 依赖于服务端实现，这种方式是一种契约

### 3、自描述消息

1.  请求头

`Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8`

第一优先顺序：`text/html` -> `application/xhtml+xml` -> `application/xml`

第二优先顺序：`image/webp` -> `image/apng`

2.  自描述消息处理器

所有的 HTTP 自描述消息处理器均在 messageConverters（类型：`HttpMessageConverter`)，这个集合会传递到 RequestMappingHandlerAdapter，最终控制写出。

messageConverters，其中包含很多自描述消息类型的处理，比如 JSON、XML、TEXT等等

以 application/json 为例，Spring Boot 中默认使用 Jackson2 序列化方式，其中媒体类型：application/json，它的处理类 MappingJackson2HttpMessageConverter，提供两类方法：

1) 读read* ：通过 HTTP 请求内容转化成对应的 Bean

2) 写write*： 通过 Bean 序列化成对应文本内容作为响应内容

### 4、扩展自描述消息

1.  实现 AbstractHttpMessageConverter 抽象类

1)  supports 方法：是否支持当前POJO类型

2)  readInternal 方法：读取 HTTP 请求中的内容，并且转化成相应的POJO对象（通过 Properties 内容转化成 JSON）

3)  writeInternal 方法：将 POJO 的内容序列化成文本内容（Properties格式），最终输出到 HTTP 响应中（通过 JSON 内容转化成 Properties ）  

2.  HttpMessageConverter 执行逻辑：

读操作：尝试是否能读取，canRead 方法去尝试，如果返回 true 下一步执行 read

写操作：尝试是否能写入，canWrite 方法去尝试，如果返回 true 下一步执行 write

3. 编码

1.  Properties 格式（application/properties+person)

需要扩展

```properties
person.id = 1
person.name = yan
```
2. json -> properties

请求地址：`/person/json/to/properties`

请求头

```properties
Accept=application/properties+person
Content-Type=application/json
```

请求体

```json
{
    "id":7,
    "name":"yan"
}

```

响应体

```properties
#Written by web server
#Tue Sep 18 15:35:00 CST 2018
person.name=yan
person.id=7
```

3.properties -> json

请求地址：`/person/properties/to/json`

请求头

```properties
Content-Type=application/properties+person
Accept=application/json
```

请求体

```properties
person.name=yan
person.id=7
```

响应体

```json
{
    "id":7,
    "name":"yan"
}
```

3. java 代码

1)  配置

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    public void extendMessageConverters(
            List<HttpMessageConverter<?>> converters) {

        converters.add(new PropertiesPersonHttpMessageConverter());
    }

}
```

2)  controller

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

3) 自定义HttpMessageConverter

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

1.  当 Accept 请求头未被制定时，为什么还是 JSON 来处理？

查看源码：

DelegatingWebMvcConfiguration的父类WebMvcConfigurationSupport->addDefaultHttpMessageConverters方法，可以看出messageConverters插入顺序。
从插入顺序可以看出Spring Boot 中默认使用 Jackson2 序列化方式，采用轮训的方式去逐一尝试是否可以 HttpMessageConverter<T>->canWrite(POJO) ,如果返回 true，说明可以序列化该
 POJO 对象。

2.  优先级是默认的是可以修改吗？

是可以调整的，通过extendMessageConverters 方法调整

同一个WebMvcConfigurerAdapter中的configureMessageConverters方法先于extendMessageConverters方法执行

可以理解为是三种方式中最后执行的一种，不过这里可以通过add指定顺序来调整优先级，也可以使用remove/clear来删除converter

使用converters.add(xxx)会放在最低优先级（List的尾部）

使用converters.add(0,xxx)会放在最高优先级（List的头部）

3.  为什么第一次是JSON，后来怎加了 XML 依赖，又变成了 XML 内用输出

Spring Boot 应用默认没有增加XML 处理器（HttpMessageConverter）实现，所以最后采用轮训的方式去逐一尝试是否可以 canWrite(POJO) ,如果返回 true，说明可以序列化该 POJO 对象，那么 Jackson 2 恰好能处理，那么Jackson 输出了。

## 数据库JDBC

主要内容

数据源( DataSource) : 分别介绍嵌入式数据源、通用型数据源以及分布式数据源务(Transaction) : 介绍事务原理,本地事务和分布式事务的使用场景

JDBC ( JSR-221) : 介绍JDBC核心接口,数据源、数据库连接、执行语句、事务 等核心API的使用方法

数据连接池技术

[Apache Commons DBCP]( http://commons.apache.org/proper/commons-dbcp/)

事务( Transaction )

事务用于提供数据完整性,并在并发访问下确保数据视图的一致性。

重要概念

自动提交模式( Auto-commitmode )

事务隔离级别( Transaction isolation levels )

TRANSACTION_READ_UNCOMMITTED

TRANSACTION_READ_COMMITTED

TRANSACTION_REPEATABLE_READ

TRANSACTION_SERIALIZABLE

保护点( SavePoints)

从上至下,级别越高,性能越差

Spring Transaction实现重用了JDBC API :

Isolation -> TransactionDefinition

ISOLATION_READ_UNCOMMITTED = Connection.TRANSACTION_ READ_UNCOMMITTED

ISOLATION_READ_COMMITTED = Connection.TRANSACTION_READ_COMMITTED

ISOLATION_REPEATABLE_READ = Connection.TRANSACTION_REPEATABLE_READ

ISOLATION_SERIALIZABLE = Connection.TRANSACTION_SERIALIZABLE

@Transaction

代理执行 TransactionInterceptor

可以控制rollback的异常粒度: rollbackFor() 以及noRollbackFor()

可以执行事务管理器: transactionManager()

通过API方式进行事务处理- PlatformTransactionManager

druid 监控

## 验证

主要内容

ean Validation ( JSR-303) : 介绍Java Bean验证、核心API、实现框架Hibernate Validator

Apache commons-alidator :介绍最传统Apache通用验证器框架,如:长度、邮件等方式Spring Validator :介绍Spring内置验证器API、以及自定义实现

Spring Assert API

JVM/Java assert断言

以上方式的缺点，耦合了业务逻辑，虽然可以通过HandlerInterceptor或者Filter做拦截，但是也是非常恶心的

还可以通过AOP的方式，也可以提升代码的可读性。

以上方法都有一个问题，不是统一的标准。

1. JSON校验如何办?

答:尝试变成Bean的方式

2.实际中很多参数都要校验那时候怎么写这样写会增加很多类

答:  确实会增加部分工作量，大多数场景，不需要自定义，除非很特殊情况。Bean Validation的主要缺点，单元测试不方便

## Spring Web Flux

如果采用Spring Web Flux，默认情况下，使用Netty WebServer (嵌入式)

从Spring Boot 1.4支持FailureAnalysisReporter实现

WebFlux

Mono:  0-1 Publisher ( 类似于Java 8中的Optional)

Flux:  0-N Publisher (类似于ava中的List)

传统的Servlet采用`HttpServletRequest.HttpServletResponse`

WebFlux采用: ServerRequest、 ServerResponse (不再限制于Servlet容器，可以选择自定义实现，  比如Netty Web Server )

## 事件 

Java9里面API称之为Flow(流)

Publisher -> publish(1)

Subscription 

(1) :订阅消息

Subs(A)#onNext() -> Subs(B)#onNext() -> Subs(C)#onNext()

Reactive是推模式(Push)

Iterator是拉模式(Pull)

Spring / Spring Boot事件机制

设计模式

观察者模式

事件/监听器模式Spring核心接口一ApplicationEvent

ApplicationListener

Spring Cloud Config Client

`java.util.Observable`是一个发布者

`java.util.Observer`是订阅者

发布者和订阅者:1:N

发布者和订阅者:N:M

事件/监听模式

`java.util.Eventobject` :

事件对象总是关联着事件源(source)

`java.util.EventListener` :  事件监听接口(标记)

Spring / Spring Boot事件机制

Spring Boot核心事件

一ApplicationEnvironmentPreparedEvent

一ApplicationPreparedEvent

一ApplicationStartingEvent

一ApplicationReadyEvent

一ApplicationFailedEvent

可以通过`SpringApplication.addListeners(…​)`或`SpringApplicationBuilder.listeners(…​)` 方法注册它们 。
如果希望自动注册这些侦听器而不管应用程序的创建方式如何，可以将`META-INF/spring.factories`
文件添加到项目中并使用`org.springframework.context.ApplicationListener` 键引用侦听器。
`org.springframework.context.ApplicationListener = com.example.project.MyListener`


应用程序运行时，应按以下顺序发送应用程序事件：（Spring Boot使用事件来处理各种任务）

`ApplicationStartingEvent`在运行开始时发送，但在除了监听器和初始化程序的注册之外的任何处理之前发送。

`ApplicationEnvironmentPreparedEvent`当被发送`Environment`到在上下文已知被使用，但是在创建上下文之前。

`ApplicationPreparedEvent`被发送刷新开始之前，但经过bean定义已经被加载。

`ApplicationReadyEvent`在刷新之后发送一个并且已经处理了任何相关的回调以指示应用程序已准备好服务请求。

`ApplicationFailedEvent`如果在启动时异常发送

Spring事件/监听

ApplicationEvent :应用事件

ApplicationListener:应用监听器

Spring Boot事件/监听器

ConfigFileApplicationListener

管理配置文件，比如:  `application.properties`  以及`application.yml`

Java SPI: `java.util.ServiceLoader`

Spring SPI

如何控制顺序

实现Ordered 以及标记@Order

在Spring里面，数值越小，越优先

Spring Cloud事件/监听器

BootstrapApplicationListener

负责加载`bootstrap.properties`或者`bootstrap.yml`

### 问题集合

1.  用reactive web，原来mvc的好多东西都不能用了?

答:不是，

Reactive Web还是能够兼容Spring.WebMVC

2.  开个线程池事务控制用API方式?比如开始写的`Excutor.fixExcutor(5)`

答: TransactionSynchronizationManager使用大量的ThreadLocal来实现的

3.  假设一个service方法给了@Transaction标签，在这个方法中还有其他service的某个方法，这个方法没有加@Transaction，那么如果内部方法报错，会回滚吗?

答:会的，当然可以过滤掉一些不关紧要的异常noRollbackFor()

4.  webFluxConfiguration 里面的映射路径和controller里面的路径有什么区别吗

答:基本上是没有区别的，注意，不重复定义，或者URL语义有重复!

5. webFlux不是跟mvc不能一起吗， 怎么一起启动了

答:  spring-boot-starter-webmvc和spring-boot-starter-webflux 可以放在同一个应用，可是webflux不会工作，默认使用webmvc, 
webflux不会被采用。其实webflux是兼容Annotation驱动，比如
` @RequestMapping`

6. webFlux可以定义restFull吗

答:  可以的，支持的!

7. spring的老项目迁移到SpringBoot,怎么弄

答:老的XML方式采用 `@ImportResource`导入!

8.  嵌入式tomcat如何调优

答:第一种通过application.properties文件调整配置参数

第二种通过接口回调:

TomcatConnectorCustomizer

TomcatContextCustomizer

9、使用外置的Servlet容器

嵌入式Servlet容器:应用打成可执行的jar

优点:简单、便携;

缺点:默认不支持JSP、优化定制比较复杂(使用定制器`ServerProperties`、自定义`EmbeddedServletContainerCustomizer` , 
自己编写嵌入式Servlet容器的创建工厂`EmbeddedServletContainerFactory`) ;
外置的Servlet容器:外面安装Tomcat--应用war包的方式打包

#  启动配置原理

几个重要的事件回调机制

配置在META-INF/spring.factories

**ApplicationContextInitializer**

**SpringApplicationRunListener**



只需要放在ioc容器中

**ApplicationRunner**

**CommandLineRunner**



启动流程：

## 创建SpringApplication对象

```java
initialize(sources);
private void initialize(Object[] sources) {
    //保存主配置类
    if (sources != null && sources.length > 0) {
        this.sources.addAll(Arrays.asList(sources));
    }
    //判断当前是否一个web应用
    this.webEnvironment = deduceWebEnvironment();
    //从类路径下找到META-INF/spring.factories配置的所有ApplicationContextInitializer；然后保存起来
    setInitializers((Collection) getSpringFactoriesInstances(
        ApplicationContextInitializer.class));
    //从类路径下找到ETA-INF/spring.factories配置的所有ApplicationListener
    setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
    //从多个配置类中找到有main方法的主配置类
    this.mainApplicationClass = deduceMainApplicationClass();
}
```

![](images/搜狗截图20180306145727.png)

![](images/搜狗截图20180306145855.png)

##  运行run方法

```java
public ConfigurableApplicationContext run(String... args) {
   StopWatch stopWatch = new StopWatch();
   stopWatch.start();
   ConfigurableApplicationContext context = null;
   FailureAnalyzers analyzers = null;
   configureHeadlessProperty();
    
   //获取SpringApplicationRunListeners；从类路径下META-INF/spring.factories
   SpringApplicationRunListeners listeners = getRunListeners(args);
    //回调所有的获取SpringApplicationRunListener.starting()方法
   listeners.starting();
   try {
       //封装命令行参数
      ApplicationArguments applicationArguments = new DefaultApplicationArguments(
            args);
      //准备环境
      ConfigurableEnvironment environment = prepareEnvironment(listeners,
            applicationArguments);
       		//创建环境完成后回调SpringApplicationRunListener.environmentPrepared()；表示环境准备完成
       
      Banner printedBanner = printBanner(environment);
       
       //创建ApplicationContext；决定创建web的ioc还是普通的ioc
      context = createApplicationContext();
       
      analyzers = new FailureAnalyzers(context);
       //准备上下文环境;将environment保存到ioc中；而且applyInitializers()；
       //applyInitializers()：回调之前保存的所有的ApplicationContextInitializer的initialize方法
       //回调所有的SpringApplicationRunListener的contextPrepared()；
       //
      prepareContext(context, environment, listeners, applicationArguments,
            printedBanner);
       //prepareContext运行完成以后回调所有的SpringApplicationRunListener的contextLoaded（）；
       
       //s刷新容器；ioc容器初始化（如果是web应用还会创建嵌入式的Tomcat）；Spring注解版
       //扫描，创建，加载所有组件的地方；（配置类，组件，自动配置）
      refreshContext(context);
       //从ioc容器中获取所有的ApplicationRunner和CommandLineRunner进行回调
       //ApplicationRunner先回调，CommandLineRunner再回调
      afterRefresh(context, applicationArguments);
       //所有的SpringApplicationRunListener回调finished方法
      listeners.finished(context, null);
      stopWatch.stop();
      if (this.logStartupInfo) {
         new StartupInfoLogger(this.mainApplicationClass)
               .logStarted(getApplicationLog(), stopWatch);
      }
       //整个SpringBoot应用启动完成以后返回启动的ioc容器；
      return context;
   }
   catch (Throwable ex) {
      handleRunFailure(context, listeners, analyzers, ex);
      throw new IllegalStateException(ex);
   }
}
```

##  事件监听机制

配置在META-INF/spring.factories

**ApplicationContextInitializer**

```java
public class HelloApplicationContextInitializer implements 
ApplicationContextInitializer<ConfigurableApplicationContext> {
    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        System.out.println("ApplicationContextInitializer...initialize..."+applicationContext);
    }
}

```

**SpringApplicationRunListener**

```java
public class HelloSpringApplicationRunListener implements SpringApplicationRunListener {

    //必须有的构造器
    public HelloSpringApplicationRunListener(SpringApplication application, String[] args){

    }

    @Override
    public void starting() {
        System.out.println("SpringApplicationRunListener...starting...");
    }

    @Override
    public void environmentPrepared(ConfigurableEnvironment environment) {
        Object o = environment.getSystemProperties().get("os.name");
        System.out.println("SpringApplicationRunListener...environmentPrepared.."+o);
    }

    @Override
    public void contextPrepared(ConfigurableApplicationContext context) {
        System.out.println("SpringApplicationRunListener...contextPrepared...");
    }

    @Override
    public void contextLoaded(ConfigurableApplicationContext context) {
        System.out.println("SpringApplicationRunListener...contextLoaded...");
    }

    @Override
    public void finished(ConfigurableApplicationContext context, Throwable exception) {
        System.out.println("SpringApplicationRunListener...finished...");
    }
}

```

配置（META-INF/spring.factories）

```properties
org.springframework.context.ApplicationContextInitializer=\
com.atguigu.springboot.listener.HelloApplicationContextInitializer

org.springframework.boot.SpringApplicationRunListener=\
com.atguigu.springboot.listener.HelloSpringApplicationRunListener
```





只需要放在ioc容器中

**ApplicationRunner**

```java
@Component
public class HelloApplicationRunner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("ApplicationRunner...run....");
    }
}
```



**CommandLineRunner**

```java
@Component
public class HelloCommandLineRunner implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("CommandLineRunner...run..."+ Arrays.asList(args));
    }
}
```



#  自定义starter

starter：

​	1、这个场景需要使用到的依赖是什么？

​	2、如何编写自动配置

```java
@Configuration  //指定这个类是一个配置类
@ConditionalOnXXX  //在指定条件成立的情况下自动配置类生效
@AutoConfigureAfter  //指定自动配置类的顺序
@Bean  //给容器中添加组件

@ConfigurationPropertie结合相关xxxProperties类来绑定相关的配置
@EnableConfigurationProperties //让xxxProperties生效加入到容器中

自动配置类要能加载
将需要启动就加载的自动配置类，配置在META-INF/spring.factories
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
```

3、模式：

启动器只用来做依赖导入；

专门来写一个自动配置模块；

启动器依赖自动配置；别人只需要引入启动器（starter）

mybatis-spring-boot-starter；自定义启动器名-spring-boot-starter



步骤：

1）、启动器模块

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.atguigu.starter</groupId>
    <artifactId>atguigu-spring-boot-starter</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!--启动器-->
    <dependencies>

        <!--引入自动配置模块-->
        <dependency>
            <groupId>com.atguigu.starter</groupId>
            <artifactId>atguigu-spring-boot-starter-autoconfigurer</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
    </dependencies>

</project>
```

2）、自动配置模块

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>

   <groupId>com.atguigu.starter</groupId>
   <artifactId>atguigu-spring-boot-starter-autoconfigurer</artifactId>
   <version>0.0.1-SNAPSHOT</version>
   <packaging>jar</packaging>

   <name>atguigu-spring-boot-starter-autoconfigurer</name>
   <description>Demo project for Spring Boot</description>

   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>1.5.10.RELEASE</version>
      <relativePath/> <!-- lookup parent from repository -->
   </parent>

   <properties>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
      <java.version>1.8</java.version>
   </properties>

   <dependencies>

      <!--引入spring-boot-starter；所有starter的基本配置-->
      <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter</artifactId>
      </dependency>

   </dependencies>



</project>

```



```java
package com.atguigu.starter;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "atguigu.hello")
public class HelloProperties {

    private String prefix;
    private String suffix;

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }
}

```

```java
package com.atguigu.starter;

public class HelloService {

    HelloProperties helloProperties;

    public HelloProperties getHelloProperties() {
        return helloProperties;
    }

    public void setHelloProperties(HelloProperties helloProperties) {
        this.helloProperties = helloProperties;
    }

    public String sayHellAtguigu(String name){
        return helloProperties.getPrefix()+"-" +name + helloProperties.getSuffix();
    }
}

```

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.condition.ConditionalOnWebApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConditionalOnWebApplication //web应用才生效
@EnableConfigurationProperties(HelloProperties.class)
public class HelloServiceAutoConfiguration {

    @Autowired
    HelloProperties helloProperties;
    @Bean
    public HelloService helloService(){
        HelloService service = new HelloService();
        service.setHelloProperties(helloProperties);
        return service;
    }
}

```
## Spring Boot Admin

[官方文档](http://codecentric.github.io/spring-boot-admin/current/#set-up-admin-server)

应用程序向我们的Spring Boot Admin Client注册（通过HTTP）或使用SpringCloud（例如Eureka，Consul）发现。UI只是Spring Boot 
Actuator端点上的AngularJs应用程序

Spring Boot管理服务器可以使用Spring Clouds DiscoveryClient来发现应用程序。优点是客户不必包括spring-boot-admin-starter-client。
您只需要向DiscoveryClient管理服务器添加一个实现 其他一切都由AutoConfiguration完成

