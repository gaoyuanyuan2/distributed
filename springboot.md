## SpringBoot
### 1、特性
<br>1. JAVA8
<br><br>2. Web flux
<br><br>1) 函数编程: Java 8 Lambda
<br><br>2) 响应编程: Reactive Streams
<br><br>3) 异步编程: Servlet 3.1或Asyc NIO(异步非阻塞提高吞吐量)
<br><br>3 爬虫式编程
<br><br>4. 函数式断点，rest暴露
<br><br>5. SpringBoot的三种启动方式:
<br><br>1) IDE启动
<br><br>2) 项目文件下，执行mvn spring-boot:run命令
<br><br>3) 项目文件下，执行mvn install，然后target目录下找到jar文件，执行java -jar jar文件
<br><br>6. SpringBoot特点
<br><br>1) 基于spring ,使开发者快速入门,门槛很低。(Spring全家桶)
<br><br>2) SpringBoot可以创建独立运行的应用而不依赖于容器
<br><br>3) 不需要打包成war包,可以放入tomcat中直接运行
<br><br>4) 提供可视化的相关功能,方便监控,比如性能,应用的健康程度等
<br><br>5) 简化配置,不用再看过多的xml
<br><br>6) 为微服务SpringCloud铺路 , SpringBoot可以整合很多各式各样的
<br><br>7) 框架来构建微服务,比如dubbo , thrift等等
### 2、Controller的使用
<br>@ PathVariable 获取url中的数据
<br>@ RequestParam  获取请求参数的值
<br>@GetMapping 组合注解
### 3、热部署
```xml
<!--热部署-->
<!--  devtcdevtools可以实现页面热部署(即页面修改后会立即生效，这个可以直接在application. properties文件中配置spring  ymeleaf.cache-false来实现) -->
<!--实现类文件热部署(类文件修改后不会立即生效)，实现对属性文件的热部署。-->
<!--即devtools会监听classpath下的文件变动，并且会立即重启应用(发生在保存时机) ,注意:因为其采用的虚拟机机制，该项重启是很快的-->
<!-- (1) base classloader (Base类加载器) ;加载不改变的Class. 例如:第三方提供的jar包。 -->
<!--(2) restart classloader (Restart类加载器) :加载正在开发的Class.-->
<!--为什么重启很快，因为重启的时候只是加载了在开发的Class,没有重新加载第三方的jar包。-->
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
### 4、日志
<br>1. SpringBoot :底层是Spring框架, Spring框架默认是用JCL 
 <br>SpringBoot选用SLF4j和(日志门面)logback(日志实现) ;
<br><br>2. 日志的级别
<br>由低到高  trace<debug<info<warn<error
### 5、定时任务






















