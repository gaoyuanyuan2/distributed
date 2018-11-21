## 性能优化
#### QPS: 每秒钟处理完请求的次数
#### TPS：每秒钟处理完的事务次数
[Java内存和CPU监视工具和技术](https://dzone.com/articles/java-memory-and-cpu-monitoring-tools-and-technique)
[内存泄漏](https://dzone.com/articles/how-memory-leaks-happen-in-java-apps)
[如何阅读线程转储](https://dzone.com/articles/how-to-read-a-thread-dump)
### 1、CPU
<br>1.  top  查看进程
<br>按1 可以看cpu信息
<br>按shift + h 看线程
<br><br>2.  top -H -p  17343 检查进程内运行的线程
<br><br>3.  cat /proc/cpuinfo 查看cpu
<br><br>
http://man7.org/linux/man-pages/man1/top.1.html

<br><br>查找线程一直占用cpu的问题
<br>curl localhost:8080/hello
<br>cpu 都被占了，很卡，但是能用。因为会切换。
<br>jstack 38983 > a.txt （线程tack）
<br>printf "%x \n " 12455线程号转16进制  查询线程号即可
<br>grep -A 5 12af a.txt
<br>简写：jstack 38983 | grep '12af' -C5 --color

<br><br>4.  vmstat
<br>vmstat 1 每秒看cpu
<br><br>http://www.man7.org/linux/man-pages/man8/vmstat.8.html

### 2、Memory
<br>free -g 看内存g
<br>free -m 看内存m
<br>df -h james/ 查看文件大小， -h 人能看懂的
<br>df -h 查看整个文件系统占有率
<br><br>http://www.man7.org/linux/man-pages/man1/free.1.html

### 3、IO
<br>iostat 1
<br>iostat -dx 1
<br>%util io cup占用多少百分比做io操作
<br><br>http://www.man7.org/linux/man-pages/man1/iostat.1.html

### 4、NetWork
<br>nicstat 查看网卡

### 5、测试
<br>1.  秒杀 js过滤99%、Nginx过滤99% 
<br><br>2.  业务梳理比技术好
<br><br>3.  99% 都是数据库问题
<br><br>3.  AB局部更新，防压力
<br><br>4.  push
<br><br>5.  能沟通解决问题就不用用技术

### 6、JVM（JDK>JRE>JVM）
<br>1.  软件层面机器码的翻译
<br><br>2.  内存管理
<br><br>3.  永久代溢出，Meta Space 自动扩容
<br><br>4.  对象生命周期不一样所以分代

#### 栈
<br>1.栈描述的是方法执行的内存模型。每个方法被调用都会创建一个栈帧(存储局部变量、操作数、方法出口等)
<br><br>2.JVM为每个线程创建一个栈 ,用于存放该线程执行方法的信息(实际参数、局部变量等)
<br><br>3.栈属于线程私有，不能实现线程间的共享!
<br><br>4.栈的存储特性是“先进后出,后进先出”
<br><br>5.栈是由系统自动分配,速度快!栈是一个连续的内存空间! 
<br><br>  动态链接：方法调用。
#### 堆
<br>堆的特点如下: 
<br><br>1.堆用于存储创建好的对象和数组(数组也是对象)
<br><br>2.JVM只有一个堆，被所有线程共享
<br><br>3.堆是一个不连续的内存空间,分配灵活,速度慢!
<br><br>GC管理的主要区域，基本采用分代收集算法。
#### 方法区
<br>方法区(又叫静态区)特点如下:
<br><br>1.JVM只有一个方法区，被所有线程共享!
<br><br>2.方法区实际也是堆，只是用于存储类、常量相关的信息!
<br><br>3.用来存放程序中永远是不变或唯一的内容。 (类信息[Class对象]、静态变量、字符串常量等)
#### 执行引擎
<br>最核心的组件，它负责执行虚拟机的字节码。一般会先编译成机器码后执行。
#### 直接内存
<br>并不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范定义的内存区域,
<br>但这部分内存也被频繁的使用。NIO引入一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，
<br>它可以使用Native函数库直接分配堆外内存，提高性能,避免了在Java堆和Native堆中来回复制数据。
#### 程序计数器 （多线程切换）
<br>是一块较小的内存空间，它可以看作是当前线程所执行的字节码的行号指示器。在虚拟机的概念模型里，
字节码解释器工作时候是通过改变这个计数器的值来选取下一条需要执行的字节码指令、分支、循环、跳转、异常处理、
线程恢复等基础功能都需要依赖这个计数器来完成。
<br><br>
![JVM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/27.png) 

<br><br>
![JMM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/28.png) 
<br><br>
#### 什么样的对象需要被GC
<br><br>1.  引用计数法
<br><br>2.  可达性分析GCRoots （解决相互引用无法回收的问题）
    <br>虚拟机栈中本地变量表引用的对象方法区中
    <br>类静态变量引用的对象常量引用的对象
    <br>本地方法栈中JNI引用的对象
<br><br>3.  不可达是不是就一定会被回收
    <br>finalize()
    
<br><br>
![回收算法](https://github.com/gaoyuanyuan2/distributed/blob/master/img/29.png) 
<br><br>

#### 参数说明
<br>-Xms20M  starting (堆的起始大小)
<br><br>-Xmx     max  (堆的最大大小)
<br><br>-Xmn     new  (堆的新生大小)

<br><br>1. 对象分配eden
<br>-XX:SurvivorRatio=8
<br>8:1:1

<br><br>TLAB  Thread Local Allaction Buffer
(栈上分配，不存在并发，减少并发争夺内存，避免锁)

<br><br>2.  对象很大
	<br>-XX:PretenureSizeThreshold=3145728   3M
<br><br>3.  长期存活的对象 
	<br>-XX:MaxTenuringThreshold=15
<br><br>4.  动态对象年龄判定
	<br>相同年龄所有对象的大小总和 > Survivor空间的一半
	
<br><br>5.  分配担保
	<br>Minor GC 之前检查 老年代最大可用连续空间是否>新生代所有对象总空间

<br><br>Minor GC  
<br><br>Major GC
<br><br>Full  GC
	
#### 什么样的对象需要回收？

#### 引用
<br>强  Object object = new Object();
<br>软  (全局变量，优化缓存，value软引用（内存不够，下一次gc会被回收）)
<br>弱  
<br>虚  (引用会被通知)
	
#### 回收
<br>1.  方法论
    <br>标记-清除算法
    <br>复制回收算法（新生代）
    <br>标记-整理算法
<br><br>2.  垃圾收集器（对算法的实现）
    <br>STW  Stop The World
    <br>Serial
    <br>ParNew 
        <br>-XX:ParallelGCThreads
    <br><br>Parallel Scavenge （全局）
        <br>吞吐量 = 运行用户代码时间 / （运行用户代码时间  + 垃圾收集时间）
        <br>-XX:MaxGCPauseMillis=n
        <br>-XX:GCTimeRatio=n
        <br>-XX:UseAdaptiveSizePolicy   GC  Ergonomics （old区长期高于某个比例，会触发能效gc）
    <br><br>Serial Old
        <br>CMS备用预案  Concurrent Mode Failusre时使用（full gc 一直回收不掉，走这个）
        <br>标记-整理算法
    <br><br>Parallel Old
        <br>标记-整理算法
    <br><br>CMS
        <br>标记-清除算法
        <br>减少回收停顿时间
        <br>碎片 -XX:CMSInitiationgOccupancyFraction  
        <br>Concurrent Mode Failure 启用Serial Old
        <br><br>-XX:+UseCMSCompactAtFullCollection
        <br>-XX:CMSFullGCsBeforeCompaction 执行多少次不压缩FullGC后 来一次带压缩的 0 表示每次都压
        <br>-XX:+UseConcMarkSweep
    <br><br>G1
<br><br>回收的时间节点
	
<br><br>如何查看当前的垃圾回收器
	<br>-XX:+PrintFlagsFinal
	<br>-XX:+PrintCommandLineFlags
	<br>server client
	<br>MBean
	
<br><br>GC日志
	<br>1.输出日志
	<br>-XX:+PrintGCTimeStamps 
	<br>-XX:+PrintGCDetails 
	<br>-Xloggc:/home/administrator/james/gc.log
	<br>-XX:+PrintHeapAtGC
	<br><br>2.日志文件控制
	<br>-XX:-UseGCLogFileRotation
	<br>-XX:GCLogFileSize=8K
	<br><br>3.怎么看
	
<br><br>JDK自带的 监控工具
<br>https://docs.oracle.com/javase/8/docs/technotes/tools/windows/toc.html
	<br>jmap -heap pid 堆使用情况
	<br>jstat  -gcutil pid 1000
	<br>jstack  线程dump 
	<br>jvisualvm
	<br>jconsole
	<br>gc.log
<br><br>MAT
	<br>http://help.eclipse.org/oxygen/index.jsp?topic=/org.eclipse.mat.ui.help/welcome.html
	<br>-XX:+HeapDumpOnOutOfMemoryError 
	<br>-XX:HeapDumpPath=/home/administrator/james/error.hprof

<br><br>怀疑：
	<br><br>1.看GC日志  126719K->126719K(126720K)
	<br><br>2.dump
	<br><br>3.MAT
		<br>1.占用Retained Heap
		<br>2.看有没有GC Root指向
	<br><br> deadlock BLOCKED
<br><br> jps 查看进程号
<br>kill 进程号	

<br><br>
![JVM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/47.jpg) 

<br><br>
![JMM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/48.jpg) 
<br><br>

<br><br>VM参数
<br>http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html


<br><br>安全点
<br><br>GC优化	 
https://mp.weixin.qq.com/s/t1Cx1n6irN1RWG8HQyHU2w

<br><br>swap  https://blog.csdn.net/u010602357/article/details/54286346?tdsourcetag=s_pctim_aiomsg

<br><br> MAT  http://www.eclipse.org/mat/

<br><br>https://github.com/oldmanpushcart/greys-anatomy

<br><br>https://note.youdao.com/share/?id=80df2676169cb223861869fb2a6017e5&type=note#/

### 7、Tomcat
<br>servlet jsp rest wbsocket 
#### Tomcat架构
<br>1.  目录结构:介绍Tomcat目录结构,如`config`、`webapps` 等目录的用途,和各种配置文件的使用场景,如`server.xml`  `context.xm1` 以及`catalina.policy` 等
<br><br>2.  应用上下文:理解Web应用上下文与Servlet之间的关系,掌握上下文路径以及其他相关属性的使用场景连接器:了解阻塞式I/O以及非阻塞式I/O的配置方式,同时掌握请求连接的设置方法
<br><br>3.  线程池:了解Tomcat线程池的配置方式以及理解它与Java标准实现的关系
<br><br>4.  Java EE组件: JMX、 JDNI架构以及使用方法。

#### 嵌入式Tomcat
<br>1.  Maven插件:利用Maven Tomcat插件构建嵌入式Tomcat容器
<br><br>2.  API编程:通过Tomcat API的角度,深入理解Tomcat运行机制以及组件之间的关系。
<br><br>3.  Spring Boot :深入探究Spring Boot利用嵌入式Tomcat API如何实现自定义容器

#### 架构图
<br><br>![整体架构](https://github.com/gaoyuanyuan2/distributed/blob/master/img/30.png) 

#### 目录结构

<br>1.  conf目录

<br><br>`catalina.policy` : Tomcat安全策略文件,控制JVM相关权限,具体可以参考`java.security.Permission`

<br><br>`catalina.properties`: Tomcat Catalina行为控制配置文件,比如Common Classloader

<br><br>`logging.properties`: Tomcat日志配置文件, JDK Logging

<br><br>`server.xml`: Tomcat Server配置文件

<br> `GlobalNamingResource`:  全局JNDI资源

<br><br>`context.xml`:  全局Context配置文件

<br><br>`tomcat-users.xml` : Tomcat角色配置文件, = ( Realm文件实现方式)

<br><br>`web.xml`  : Servlet标准的web.xml部署文件, Tomcat默认实现部分配置入内:

<br>`org.apache.catalina.servlets.DefaultServlet`

 <br>`org.apache.jasper.servlet.JspServlet`

<br><br>2.  lib目录

<br> Tomcat存放公用类库

<br> `ecj-*.jar` : Eclipse Java编译器

<br>`jasper.jar` : JSP编译器

<br><br>3.  logs目录

<br>`localhost.${date}.log` :当Tomcat应用起不来的时候,多看该文件,比如:类冲突

<br> `NoClassDefFoundError`

<br>`ClassNotFoundException`
     
<br>`catalina.${date}.log`: 控制台输出，`System.out` 外置
<br><br>4.  webapps 
<br><br>5.  work
#### 部署
<br>1.  放置在webapps目录
<br>直接拖过去
<br><br>2.  修改 `conf/server.xml`
<br>添加Context元素:
```xml
<Context docBase=" ${webAppAbsolutePath}" path="/" reloadable="true" />
<Context docBase=" ${webAppAbsolutePath}" path="/tomcat" reloadable="true" />
```

<br><br>熟悉配置元素可以参考`org.apache.catalina.core.StandardContext`  setter方法
<br>`Container`
<br>`Context`
<br><br>该方式不支持动态部署,建议考虑在生产环境使用。

<br><br>3.  独立 `context` xml配置文件
<br>首先注意 `conf\Catalina\localhost`
<br>独立context XML配置文件路径: `${TOMCAT_ HOME}/conf/Catalina/localhost+${ContextPath}.xml`
<br>注意:该方式可以实现热部署、热加载，因此建议在开发环境使用。

<br><br>热部署：`reloadable="true" `
```xml
<Context docBase="E:/Downloads/tomcat/target/tomcat-1.0-SNAPSHOT" reloadable="true" />
```

<br><br>4.  改appBase
```xml
<Host name="localhost" unpackWARs="true" appBase="webapps" autoDeploy="true ">
```

#### 连接器
<br>参考文件: https://tomcat.apache.org/tomcat-7.0-doc/config/http.html
<br><br>实现类:  `org.apache.catalina.connector.Connector`
<br><br>
![Connector Comparison](https://github.com/gaoyuanyuan2/distributed/blob/master/img/31.png) 
<br><br>
#### 编码
<br>默认 ISO-8859-1
<br><br>解决编码问题
```xml
<Connector port="8080"  protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" URIEncoding="UTF-8"/>
```
```java
req.setCharacterEncoding("UTF-8");
resp.setCharacterEncoding("UTF-8");
resp.setContentType("text/html ;charset=UTF-8");
```

#### 问答互动
<br>1.  如果配置path的话是以文件名为主还是以配置的为主
<br> 独立context XML配置文件时,设置`path` 属性是无效的。
<br><br>2.  根独立context XML配置文件路径
<br>`${TOMCAT_ HOME }/conf/${Engine.name }/${HOST.name }/ROOT.xm1`
<br><br>3.  如果实现热部署
<br>调整`<context>` 元素中的属性`reloadable="true"`   
<br><br>4.  连接器里面的线程池是用的哪个线程池
    <br>注意`conf/server.xm1l`文件中的一段注释:
 ```xml
 <Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1" connectionTimeout=" 20000" 
 redirectPort="8443"/>
```
```java

import  org.apache.catalina.Executor;

public interface Executor extends java.util.concurrent.Executor, Lifecycle{

      public String getName();

      void execute( Runnable command, long timeout, TimeUnit unit);
}
```
<br>标准实现: `org.apache.catalina.org.StandardThreadExecutor`将连接处理交付给Java标准线程池:
<br>`org.apache.tomcat.util.threads.ThreadPoolExecutor`
<br><br>后面版本用队列，生产者，消费者。

<br><br>5.  INDI能不能稍微说下之前只是在数据源的时候用过,但是不是太理解
```xml
<Context ...
    <Resource name= "mail/Session" auth="Container" type="javax.mail.Session" mail.smtp .host="localhost"/>
</Context>
```
```java
    Context  initCtx =  new InitialContext();
    
    Context envCtx =  (Context)initCtx.lookup("java:comp/env");
    Session  session = (Session) envCtx.lookup( mail/Session");
    
    Message message = new MimeMessage(session) ;
    
    message.setFrom( newInternetAddress ( request. getParameter("from" )));
    InternetAddress to[] = new InternetAddress [1];
    
    to[0] = new InternetAddress(request.getParameter("to"));
    message.setRecipients(Message RecipientType.To, to);
    message.setSubject(request.getParameter("subject"));

```


#### Web技术栈
#### Servlet技术栈
<br>servlet 容器启动spring
#### Web Flux ( Netty )
#### BIO NIO
<br>NIQ并非一定能够提高性能,比如请求数据量较大, NIO性能比BIQ还要差
<br>NIQ多工，读、写,同步的
<br>数量多效率高，数据量高反而效率低
#### Tomcat Maven插件
#### Tomcat API接口
#### Spring Boot嵌入式Tomcat 

#### Web自动动装
<br>1.  API角度分析
<br>Servlet3.0 + API实现  `ServletContainerInitializer`

<br><br>2.  容器角度分析
<br>传统的Web应用,将webapp部署到Servlet容器中。
<br>嵌入式Web应用,灵活部署,任意指定位置(或者通过复杂的条件判断)
<br>Tomcat 7是Servlet 3.0的实现，`ServletContainerInitializer`
<br>Tomcat 8是Servlet 3.1的实现， NIO `HttpServletRequest` 、`HttpServletResponse`

#### jar启动
<br>`java -jar` 或者`jar` 读取`.jar`
<br>参考JDKAPI : `java.util.jar.Manifest`

<br><br>META-INF /MANIFEST.MF , 其中属性Main-Class 就是引导类所在。

####  Tomcat Maven插件
<br>1.  Tomcat 7 Maven插件
```xml
<groupId>org.apache.tomcat.maven</groupId>
<artifactId>tomcat7-maven-plugin</artifactId>
<version>2.1</version>
```
```properties
Manifest-Version: 1.0
Main-Class: org.apache.tomcat.maven.runner.Tomcat7RunnerCli
```

得出Tomcat 7可执行jar引导类是
`org.apache.tomcat.maven.runner.Tomcat7RunnerCli`

#### Tomcat API接口
#### Embedded
#### TomcatService
#### Engine
#### Host
#### ConnectorContext
<br>1.  创建Tomcat实例
<br>`org.apache.catalina.startup.Tomcat`
<br>Maven坐标: `org. apache. tomcat . embed : tomcat-embed-core:7.0.37`
<br><br>2.  设置Host对象
```java
Host  host  =  tomcat.getHost() ;
host.setName("localhost");
host.setAppBase("webapps");|
```
<br><br>3.  设置Classpath
<br>Classpath读取资源:配置、类文件
<br>conf/web. xm1作为配置文件,并且放置Classpath目录下(绝对路径)
<br><br>4.  设置DemoServlet
```java
//添加DemoServlet到Tomcat容器
Wrapper wrapper = tomcat.addServlet(contextPath, "DemoServlet", newDemoServlet());
wrapper.addMapping("/demo");
```
#### Spring Boot 嵌入式 Tomcat
#### EmbeddedServletContainerCustomizer
#### ConfigurableEmbeddedServletContainer
#### EmbeddedServletContainer
#### TomcatContextCustomizerTomcatConnectorCustomizer

#### Tomcat配置调优
<br>1.  减少配置优化
<br><br>场景一:假设当前应REST应用(微服务)
<br><br>分析:它不需要静态资源, Tomcat容器静态和动态
<br>静态处理:`DefaultServlet`
<br>优化方案:通过移除`conf/web.xm1`中`org.apache.catalina.servlets.DefaultServlet`
<br>动态: `JspServlet`
<br>优化方案:通过移除`conf/web.xm1`中`org.apache.jasper.servlet.JspServlet`
<br>`DispatcherServlet` : Spring Web MVC应用Servlet 
<br>`jspServlet` :编译并且执行lsp页面
<br>`DefaultServlet` : Tomcat处理静态资源的Servlet
<br>Last-Modified:如果服务器端的资源没有变化，则自动返回 HTTP 304 （Not Changed.）状态码，内容为空，这样就节省了传输数据量
<br><br>2.  移除welcome-file-list
```xml
<welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file> index.htm</welcome-file>
    <welcome-file> index.jsp</welcome-file>
</welcome-file-list>
```
<br><br>3.  移除Session设置
<br>如果程序是REST JSON Content-Type或者MIME Type : application/json。移除Session设置
<br>对于微服务/REST应用,不需要Session ,因为不需要状态。Spring Security QAuth 2.0、JWT
<br>Session通过jsessionld进行用户跟踪, HTTP无状态,需要一一个ID与当前用户会话联系。Spring Session HttpSession jessionld作为Redis ,实现多个机器登录,用户会话不丢失。
<br>存储方法: Cookie、URL 重写、  SSL。
<br><br>4.  移除Valve 
<br>Valve 类似于Filter
<br>移除AccessLogValve, 可以通过Nginx的Access Log替代，Valve |实现都需要消耗Java应用的计算时间。

<br><br>场景二:需要JSP的情况

<br><br>分析: JspServlet无法，了解JspServlet处理原理
<br><br>Servlet周期:
<br>实例化: Servlet和Filter实现类必须包含默认构造器。反射的方式进行实例化。
<br>初始化: Servlet容器调用Servlet或Filter init()方法
<br>销毁: Servlet容器关闭时, Servlet或者Filter destroy()方法被调用
S<br>ervlet或者Filter 在一个容器中 ,是一般情况在一个Web App中是一个单例 ,不排除应用定义多个。
<br>JspServlet相关的优化ServletConfig 
<br><br>参数:
<br><br>需要编译
<br>compiler
<br>modificationTestInterval
<br><br>不需要编译
<br>development 设置false
<br>development = false , 那么,这些JSP要如何编译。
<br><br>优化方法:
<br>Ant Task执行JSP编译
<br>Maven插件: `org.codehaus.mojo:jspc-maven-plugin`
```xml
<dependency>
    <groupId>org.apache.sling</ groupId>
    <artifactId>jspc-maven-plugin</artifactId>
    <version>2.1.0</version>
</dependency>
```
<br><br>JSP ->翻译.jsp或者.jspx文件成.java ->编译.class
<br><br>总结, `conf/web.xml`  作为Servlet应用的默认`web.xml` ,实际上,应用程序存在两份`web.xm1` ,其中包括应用的`web.xml`,最终将两者合并。
<br><br>JspServlet如果development参数为true ,它会自定检查文件是否修改,如果修改重新翻译,再编译(加载和执行)。言外之意, JspServlet开发模式可能会导致内存溢出。卸载Class不及时所知道Perm区域不够。

<br><br>JspServlet如果development参数为true ,它会自定检查文件是否修改,如果修改重新翻译,再编译(加载和执行)。言外之意, JspServlet开发模式可能会导致内存溢出。卸载Class不及时会导致Perm区域不够。
<br>ParentClassLoader -> 1.class 2.class 3.class
<br>ChildClassLoader -> 4.class , 5.class
<br>ChildClassloaderload    1-5.class
<br>1.class需要卸载,需要将ParentClassLoader设置为null ,当Classloader被GC后, 1-3 class全部会被卸载。
<br>1.class它是文件,文件被JVM加载, 二进制> Verify->解析

#### 修改连接池数量
<br>通过server.xml
```xml
 <Connector executor="tomcatThreadPool"
               port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
                       maxThreads="150" minSpareThreads="4"/>
```

<br>通过程序来理解，`Executor`实际的Tomcat 接口:
<br>`org.apache.catalina.Executor`
<br>扩展:J.U.C标准接口`java.util.concurrent.Executor`
<br>实现: `org.apache.catalina.core.StandardThreadExecutor`
<br>线程数量
```java
     /**
     * max number of threads
     */
    protected int maxThreads = 200;

    /**
     * min number of threads
     */
    protected int minSpareThreads = 25;
    
    public void setMaxThreads(int maxThreads) {
        this.maxThreads = maxThreads;
        if (executor != null) {
            executor.setMaximumPoolSize(maxThreads);
        }
    }
    
    public void setMinSpareThreads(int minSpareThreads) {
        this.minSpareThreads = minSpareThreads;
        if (executor != null) {
            executor.setCorePoolSize(minSpareThreads);
        }
    }
```
<br>线程池:
<br>`org.apache.tomcat.util.threads.ThreadPoolExecutor` (`java.util.concurrent.ThreadPoolExecutor`)

<br><br>总结: Tomcat 10连接器使用的线程池实际标准的Java线程池的扩展,最大线程数量和最小线程数量实际上分别是MaximumPoolSize和CorePoolSize. 

#### 通过JMX
<br>观察StandardThreadExecutor是否存在调整线程池数量的API
<br><br>评估一些参考:
<br><br>1.正确率
<br><br>2. Load ( CPU-> JVM GC )
<br><br>3. TPS/QPS(越大越好)
<br><br>4. CPU密集型(加密/解密、算法)
<br><br>5. 1/O密集型,网络、文件读写等


#### 问题:到底设置多少的线程数量才是最优?

<br><br>首先,评估整体的情况量,假设100W QPS ,有机器数量100台,每台支撑1w QPS。

<br><br>第二,进行压力测试，需要一些测试样本, JMeter来实现,假设一次请求需要RT 10ms，1秒可以同时完成100个请求。10000/ 100= 100线程。

<br>确保, Load太高。减少Full GC, GC取决于JVM堆的大小。  执行一次操作需要5MB内存，50GB。

<br>20 GB内存,必然执行GC。要不调优程序,最好对象存储外化,比如Redis ,同时又需要评估Redis网络开销。又要评估网卡的接受能力。

<br><br>第三,常规性压测,由于业务变更,会导致底层性能变化。


<br><br>默认算法
```jshelllanguage
java -jar -server -XX:-PrintGCDetails -Xloggc: ./1g/gc.1og -XX: +HeapDumpOnOutOfMemoryError 
-Xms1g -Xmx1g -XX:MaxGCPauseMillis=250 -Djava.awt.headless=true stress-test-demo-0.0.1-SNAPSHOT.jar
```

<br><br>G1算法
```jshelllanguage
java -jar -server -XX:-PrintGCDetails -Xloggc: ./1g/g1-gc.1og -
XX:+He apDumpopoutOfMemoryError -Xms1g -Xmx1g -XX:+UseG1GC -XX: +UseNUMA -
XX:MaxGCPauseMillis=250 -Djava . awt . headless=true stress-test-demo-e .0.1-SNAPSHOT.jar
```

<br>-server 主要提高吞吐量,在有限的资源,实现最大化利用
<br>-client 主要提高响应时间,主要是提高用户

<br><br>SpringBoot

<br>`application. properties`

```properties

#<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"maxThreads="9" minSpareThreads="9"/>

#线程池大小
server.tomcat.maxThreads = 99

server.tomcat.minSpareThreads = 9

#取消Tomcat AccessLogValve

server.tomcat.accesslog.enabled = false

#取消JspServlet
server.jspServlet.registered=false
```

#### 程序调优
#### JVM参数调优

### 8、性能优化MySQL
#### 1. 架构图
![架构图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/32.png) 

![架构图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/33.png) 

![架构图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/34.png) 

##### 引擎介绍 

|比较类别 |Innodb |Myisam|
|:--:|:--:|:--:|
|存储文件| .frm 表定义文件 .ibd 数据文件|    .frm表定义文件 .myd数据文件  .myi索引文件|
|锁 |表锁、行锁|  表锁|
|事务 |ACID| 不支持|
|CRDU |读、写|  读多|
|count| 扫表| 专门存储的地方|
|索引结构| B+ Tree |B+ Tree|

#### 2. 性能
<br><br>1. 影响性能的因素
<br><br>1) 人为因素-需求
<br>count(*) 论坛实时、 准实时、有误差
<br><br>2) 程序员因素一面向对象
<br><br>3) Cache
<br><br>4) 对可扩展过度追求
<br><br>5) 表范式
<br><br>6) 应用场景
<br><br>OLTP On-Line Transaction Processioning
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/35.png) 
<br><br>
<br>IO per second
<br><br>OLAP  On-Line Analysis Processing
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/36.png) 
<br><br>
<br><br>2. 提高性能
<br><br>1) 索引
<br>索引结构
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/37.png) 
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/38.png) 
<br><br>
<br>B+ Tree 自增id做索引效率更高
<br><br>索引有序，排序效率高
<br><br>查看缓存 
```sql
show variables like '%sort%'
```
<br><br>group by 基于排序（选排序再分组）
<br><br>distinct 基于group by

<br><br>3. 锁
<br>行锁
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/39.png) 
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/40.png) 
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/41.png) 
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/42.png) 
<br><br>
<br><br>无索引自动升级为表锁
<br><br>死锁
```sql
-- 释放锁
unlock tables 
-- 写锁
lock table user write
-- 间隙锁(范围锁)
update user set name = 'xxx' where id > 1 and id < 4 
```
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/46.png) 
<br><br>
<br><br>4. 优化
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/43.png) 
<br><br>
<br><br>1)QEP Query Execution Plan
<br><br>使用 加上explain
#### 我们先看一下在MySQL Explain功能中给我们展示的各种信息的解释
<br>1. ID: Query 0ptimizer所选定的执行计划中查询的序列号;
<br><br>2. Select_type: 所使用的查询类型，主要有以下这几种查询类型
<br><br>DEPENDENT SUBQUERY: 子查询中内层的第一个 SELECT，依赖于外部查询的结果集;
<br><br>DEPENDENT UNION:子查询中的UNION，且为UNION中从第二个SELECT开始的后面所有
<br><br>SELECT，同样依赖于外部查询的结果集;
<br><br>PRIMARY:子查询中的最外层查询，注意并不是主键查询;SIMPLE:除子查询或者UNION之外的其他查询;
<br><br>SUBQUERY:子查询内层查询的第一个SELECT，结果不依赖于外部查询结果集;UNCACHEABLE SUBQUERY:结果集无法缓存的子查询;
<br><br>UNION: UNION 语句中第二个SELECT开始的后面所有SELECT，第一个SELECT为PRIMARYUNION RESULT: UNION 中的合并结果;
<br><br>3. Table: 显示这一步所访问的数据库中的表的名称:
<br><br>4. Type:告诉我们对表所使用的访问方式，主要包含如下集中类型:<
<br>all: 全表扫描
<br>const:读常量，且最多只会有一条记录匹配，由于是常量，所以实际上只需要读一次;
<br>eq_ ref: 最多只会有一条匹配结果，一般是通过主键或者唯一  键索引来访问;O fulltext:
<br>index:全索引扫描;
<br>index_merge:查询中同时使用两个(或更多)索引，然后对索引结果进行merge之后再读取表数据:
<br>index_subquery:子查询中的返回结果字段组合是一个索引(或索引组合)，但不是一个主键或者唯一索引:
<br>rang:索引范围扫描;
<br>ref: Join 语句中被驱动表索引引用查询:
<br>ref_or_null:与ref的唯-区别就是在使用索引引用查询之外再增加一个空值的查询
<br>system:系统表，表中只有一行数据:
<br>unique_subquery: 子查询中的返回结果字段组合是主键或者唯-约東;
<br><br>依次从好到差: system , const , eq_ ref , ref , fulltext, ref_ or_ null ,
unique_subquery ，index_ subquery , range , index_ merge , index , ALL
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/44.png) 
<br><br>5. Possible_ keys:该查询可以利用的索引.如果没有任何索引可以使用，就会显示成null,这一项内容对于优化时候索引的调整非常重要;
<br><br>6.  Key: MySQL Query Optimizer 从possible. keys 中所选择使用的索引;Key_ len:被选中使用索引的索引键长度;
<br><br>7.  Ref:列出是通过常量(const) ，还是某个表的某个字段(如果是join)来过滤(通过key)的;
<br><br>8.  Rows: MySQL Query Optimizer通过系统收集到的统计信息估算出来的结果集记录条数;
<br><br>9.  Extra:  查询中每一一步实现的额外细节信息，主要可能会是以下内容: 
<br>Distinct:查找distinct值，所以当mysql找到了第一条匹配的结果后，将停止该值的查询而转为后面其他值的查询;
<br>Full scan on NULL key: 子查询中的一种优化方式， 主要在遇到无法通过索引访问null值的使用使用;
<br>Impossible WHERE noticed after reading const tables: MySQL Query Optimizer通过收集到的统计信息判断出不可能存在结果;
<br>No tables: Query 语句中使用FROM DUAL 或者不包含任何FROM 子句;
<br>Not exists: 在某些左连接中MySQL Query Optimizer所通过改变原有Query 的组成而使用的优化方法，可以部分减少数据访问次数;
<br>Range checked for each record (index map: N):通过MySQL 官方手册的描述，当MySQL Query Opt imizer没有发现好的可以使用的索引的时候，如果发现如果来自前面的表的列值已知，可能部分索引可以使用。对前面的表的每个行组合，MySQL 检查是否可以使
<br>用range或index_ merge访问方法来索取行。
<br>Select tables optimized away:当我们使用某些聚合函数来访问存在索引的某个字段的时候，MySQL Query Optimizer会通过索引而直接一次定位到所需的数据行完成整个查询。当然，前提是在Query中不能有GROUP BY操作。如使用MIN()或者MAX ()的时候;
<br>Using file sort: 当我们的Query 中包含ORDER BY操作，而且无法利用索引完成排序操作的时候，MySQL Query 0pt imizer不得不选择相应的排序算法来实现。
<br>Using index:所需要的数据只需要在Index即可全部获得而不需要再到表中取数据;
<br>Using index for group-by: 数据访问和Using index - *样，所需数据只需要读取索引即可，而当Query 中使用了GROUP BY或者DISTINCT 子句的时候，如果分组字段也在索引中，Extra 中的信息就会是Using index for group-by;
<br>Using temporary:  当MySQL 在某些操作中必须使用临时表的时候，在Extra信息中就会出现Using temporary 。主要常见于GROUP BY和ORDER BY等操作中。
<br>Using where:如果我们不是读取表的所有数据，或者不是仅仅通过索引就可以获取所有需要的数据，则会出现Using where 信息;
<br>Using where with pushed condition:  这是一个仅仅在NDBCluster 存储引擎中才会出现的信息，而且还需要通过打开Condition Pushdown优化功能才可能会被使用。控制参数为encine condition nuishdown 。
<br><br>5.  Profiling (有个概念)
```sql
set profiling=1;

select nick_ name ,count(*) from user group by nick_name;show profiles;

show profile cpu,block io for query 75;

```
<br><br>1. join \ order by \group by解释
<br><br>

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/45.png) 



