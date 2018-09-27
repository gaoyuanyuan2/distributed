### Spring Boot实际使用场景
<br>在Spring Boot 2.0.0，如果应用采用Spring Web MVC作为Web服务，默认情况下， 使用嵌入式Tomcat。
<br>如果采用Spring Web Flux，默认情况下，使用Netty WebServer (嵌入式)

### 问题集合
<br>1.用reactive web，原来mvc的好多东西都不能用了?
<br>回答:不是，
<br><br>Reactive Web还是能够兼容Spring WebMVC

### 单数据源的场景
数据连接池技术
[Apache Commons DBCP]( http://commons.apache.org/proper/commons-dbcp/)


如果采用Spring Web Flux，默认情况下，使用Netty WebServer (嵌入式)

从Spring Boot 1.4支持FailureAnalysisReporter实现

WebFlux

Mono:0- 1 Publisher ( 类似于Java 8中的Optional)

Flux:   0- N Publisher (类似于ava中的List)

传统的Servlet采用HttpServletRequest.HttpServletResponse

WebFlux采用: ServerRequest、 ServerResponse (不再限制于Servlet容器，可以选择自定义实现，  比如Netty Web Server )

事务( Transaction )
事务
事务用于提供数据完整性,并在并发访问下确保数据视图的一致性。
重要概念
自动提交模式( Auto-commitmode )
事务隔离级别( Transaction isolation levels )
保护点( Savepoints )

