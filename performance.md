## 性能优化
#### QPS: 每秒钟处理完请求的次数
#### TPS：每秒钟处理完的事务次数
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
<br><br>2.  修改 `confi/server.xml`
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
<br>独立context XML配置文件路径: `${TOMCAT_ HOME}/conf/Catalina/localhost+${ContextPath} .xml`
<br>注意:该方式可以实现热部署、热加载，因此建议在开发环境使用。

<br><br>热部署：`reloadable="true" ``
```xml
<Context docBase="E:/Downloads/tomcat/target/ tomcat-1.0- SNAPSHOT" reloadable="true" />
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
req. setCharacterEncoding("UTF-8");
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
 <Connectpr executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1" connectionTimeout=" 20000" redirectPort="8443"/>
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