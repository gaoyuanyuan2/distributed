## Netty
### 1、常见的Reactor线程模型有三种
<br>1. Reactor单线程模型
<br>Reactor单线程模型，指的是所有的I/O操作都在同一个NIO线程上面完成
<br>对于一些小容量应用场景，可以使用单线程模型，但是对于高负载、大并发的应用却不合适，主要原因如下：
<br><br>1) 一个NIO线程同时处理成百上千的链路，性能上无法支撑。即便NIO线程的CPU负荷达到100%，也无法满足海量消息的编码、解码、读取和发送；
<br><br>2) 当NIO线程负载过重之后，处理速度将变慢，这会导致大量客户端连接超时，超时之后往往进行重发，这更加重了NIO线程的负载，
最终导致大量消息积压和处理超时，NIO线程会成为系统的性能瓶颈；
<br><br>3) 可靠性问题。一旦NIO线程意外跑飞，或者进入死循环，会导致整个系统通讯模块不可用，不能接收和处理外部信息，造成节点故障。
<br><br>![单线程模型](https://github.com/gaoyuanyuan2/distributed/blob/master/img/20.png) 
<br><br>2. Reactor多线程模型
<br>Reactor多线程模型与单线程模型最大区别就是有一组NIO线程处理I/O操作
<br><br>在绝大多数场景下，Reactor多线程模型都可以满足性能需求；但是，在极特殊应用场景中，一个NIO线程负责监听和处理所有的客户端连接
可能会存在性能问题。例如百万客户端并发连接，
或者服务端需要对客户端的握手信息进行安全认证，认证本身非常损耗性能。这类场景下，单独一个Acceptor线程可能会存在性能不足问题。
<br><br>![多线程模型](https://github.com/gaoyuanyuan2/distributed/blob/master/img/21.png) 
<br><br>3. 主从Reactor多线程模型（效率最高）
<br>服务端用于接收客户端连接的不再是1个单独的NIO线程，而是一个独立的NIO线程池。Acceptor接收到客户端TCP连接请求处理完成后（可能包含接入认证等），
将新创建的SocketChannel注册到I/O线程池（sub reactor线程池）的某个I/O线程上，由它负责SocketChannel的读写和编解码工作。
Acceptor线程池只用于客户端的登录、握手和安全认证，一旦链路建立成功，就将链路注册到后端subReactor线程池的I/O线程上，有I/O线程负责后续的I/O操作。
<br><br>*它与多线程模型的主要区别在于其使用一组线程或进程在一个共享的监听套接字上accept连接*
<br><br>![主从多线程模型](https://github.com/gaoyuanyuan2/distributed/blob/master/img/22.png) 
<br><br>Boss调度资源的总管家(Dispatcher)接待大厅，用来负责轮询Selector、事件的管理(SelectionKey):while(true)轮询
<br>Worker 子线程-一个一个地去执行Handler
### 2、总结
 <br>1. 零拷贝:是指计算机操作的过程中，CPU不需要为数据在内存之间的拷贝消耗资源。而它通常是指计算机在网络上发送文件时，
 不需要将文件内容拷贝到用户空间（JVM）而直接在内核空间（Kernel Space）中传输到网络的方式。
<br><br>2. 源码 只要是方法后面加了个0的，都是实现类的方法，不是接口在Netty中提供了非常丰富的工具类。
<br><br>3. 对比
 <br>同步:相对于IO操作而言的，在同一时间，只能完成一个操作(JDK NIO)
 <br>异步:相对于I0操作而言的，在同一时间，  同时完成多个操作(JDK AIO)
 <br>阻塞:相对于数据而言，判断数据有没有准备好，如果没有准备好，停住不前（BIO  accept）
<br> 非阻塞:不管数据有没有准备好，都会给一个反馈（NIO） 
 <br><br>BIO 同步阻塞
 <br>NIO 同步非阻塞
 <br>AIO 异步非阻塞
<br><br> Netty   NIO框架(加了线程池ThreadPool )
<br> 异步非阻塞(基于NI0来实现)
 <br><br>4. pipeline 无锁化串行(链路)
 <br>Netty采用了串行无锁化设计，在I/O线程内部进行串行操作，避免多线程竞争导致的性能下降。表面上看，串行化设计似乎CPU利用率不高，并发程度不够。但是，通过调整NIO线程池的线程参数，可以同时启动多个串行化的线程并行运行，
 这种局部无锁化的串行线程设计相比一个队列-多个工作线程模型性能更优。
 <br>实现异步非常重要的一环，无锁化串行的实现 变得可控。同时执行，按顺序取结果。
<br><br> 5. Channel 与 ChannelPipeline
 <br><br>![ChannelPipeline](https://github.com/gaoyuanyuan2/distributed/blob/master/img/23.png) 
  <br>tail  inbound=true, outbound= false  尾进
  <br>head  inbound=false, outbound=true  头出
  <br>参照物:程序，channelNetty异步非阻塞IO框架
  <br>异步发生，都是在线程中响应的
  <br>我要往外面发送东西了Out 请求型的动作
  <br>业务程序来主动触发
  <br>我要开始接收东西了  In  响应型的动作
  <br>Netty来主动触发的，业务程序是被动接收(事件)
<br><br>6. 拆包 粘包 协议
<br>1) 定长，不够空格补全
<br>2) 包尾添加特殊分隔符（适合文本）
<br>3) 约定协议（将消息分为消息头和消息体）



















