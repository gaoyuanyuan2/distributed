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
<br>Worker 子线程一个一个地去执行Handler
### 2、零拷贝
 <br>1. 零拷贝:是指计算机操作的过程中，CPU不需要为数据在内存之间的拷贝消耗资源。而它通常是指计算机在网络上发送文件时，
 不需要将文件内容拷贝到用户空间（JVM）而直接在内核空间（Kernel Space）中传输到网络的方式。
### 3、 源码 
<br>只要是方法后面加了个0的，都是实现类的方法，不是接口在Netty中提供了非常丰富的工具类。
### 4、 对比
 <br>同步:相对于IO操作而言的，在同一时间，只能完成一个操作(JDK NIO)
 <br>异步:相对于I0操作而言的，在同一时间，  同时完成多个操作(JDK AIO)
 <br>阻塞:相对于数据而言，判断数据有没有准备好，如果没有准备好，停住不前（BIO  accept）
<br> 非阻塞:不管数据有没有准备好，都会给一个反馈（NIO） 
<br><br>Socket又称套接字：服务端监听、客户端请求服务器，服务器确认，客户端确认，进行通信
 <br><br>1. BIO 同步阻塞
 ```java
Socket client = server.accept();//等待客户端连接，阻塞方法
```
 <br><br>2. NIO 同步非阻塞
  <br><br>3. AIO 异步非阻塞
 NIO2.0的异步套接字通道是真正的异步非阻塞I/O,对应于UNIX网络编程中的事件驱动I/O(AIO)。
  它不需要通过多路复用器(Selector)对注册的通道进行轮询操作即可实现异步读写，从而简化了NIO的编程模型。
   通过java. util.concurrent.Future类来表示异步操作的结果;
  在执行异步操作的时候传入一个java.nio.channels。
  CompletionHandler接口的实现类作为操作完成的回调。 
 <br><br> Netty   NIO框架(加了线程池ThreadPool )
 <br> 异步非阻塞(基于NI0来实现)
 ### 5、执行流程
  <br>1. Server:
 <br><br>步骤一:打开ServerSocketChannel,用于监听客户端的连接，它是所有客户端连接的父管道
 ```java
 ServerSocketChannel ssc = ServerSocketChannel.open();
 ```
步骤二:绑定监听端口，设置连接为非阻塞模式
 ```java
 ssc.bind(new InetSocketAddress(port));
 ssc.configureBlocking(false);
 ```
步骤三:创建Reactor线程，创建多路复用器并启动线程
 ```java
this.seletor = Selector.open();
```
步骤四:将ServerSocketChannel注册到Reactor线程的多路复用器Selector.上，监听ACCEPT事件
 ```java
ssc.register(this.seletor, SelectionKey.OP_ACCEPT);
```
步骤五:多路复用器在线程run方法的无限循环体内轮询准备就绪的Key
 ```java
this.seletor.select();
Iterator<SelectionKey> keys = this.seletor.selectedKeys().iterator();
```
步骤六:多路复用器监听到有新的客户端接入，处理新的接入请求，完成TCP三次握手，建立物理链路
 ```java
SocketChannel sc = ssc.accept();
```
 步骤七:设置客户端链路为非阻塞模式
 ```java
sc.configureBlocking(false);
```
步骤八:将新接入的客户端连接注册到Reactor 线程的多路复用器上，监听读操作，读取客户端发送的网络消息
 ```java
sc.register(this.seletor, SelectionKey.OP_READ);
```
步骤九:异步读取客户端请求消息到缓冲区
 ```java
sc.read(this.readBuf);
```
步骤十:对ByteBuffer进行编解码，如果有半包消息指针reset,继续读取后续的报文，将解码成功的消息封装成Task,投递到业务线程池中，进行业务逻辑编排。
 <br><br>步骤十一:将POJO对象encode成ByteBuffer, 调用SocketChannel的异步write 接口，将消息异步发送给客户端
<br><br>2. Client
 <br><br>步骤一:打开SocketChannel,绑定客户端本地地址(可选，默认系统会随机分配一个可用的本地地址)
 ```java
sc = SocketChannel.open();
```
步骤二：设置SocketChannel为非阻塞模式，同时设置客户端连接的TCP参数
 ```java
sc.configureBlocking(false);
```
步骤三:异步连接服务端
  ```java
sc.connect(address);
```
步骤四:判断是否连接成功，如果连接成功，则直接注册读状态位到多路复用器中
 <br><br>步骤五:向Reactor线程的多路复用器注册OP_CONNECT状态位，监听服务端的TCPACK应答
 <br><br>步骤六:创建Reactor线程，创建多路复用器并启动线程
 <br><br>步骤七:多路复用器在线程run方法的无限循环体内轮询准备就绪的Key
 <br><br>步骤八:接收connect事件进行处理
 <br><br>步骤九:判断连接结果，如果连接成功，注册读事件到多路复用器
 <br><br>步骤十:注册读事件到多路复用器
 <br><br>步骤十一:异步读客户端请求消息到缓冲区
 <br><br>步骤十二:对ByteBuffer进行编解码，如果有半包消息接收缓冲区Reset, 继续读取后续的报文，将解码成功的消息封装成Task,投递到业务线程池中，进行业务逻辑编排。
 <br><br>步骤十三:将POJO对象encode成ByteBuffer, 调用SocketChannel的异步write 接口,将消息异步发送给客户端。
### 6. NIO编程的优点
<br>1. 客户端发起的连接操作是异步的，可以通过在多路复用器注册OP_ CONNECT等待后续结果，不需要像之前的客户端那样被同步阻塞。
   <br> <br>2. SocketChannel 的读写操作都是异步的，如果没有可读写的数据它不会同步等待，直接返回，
  这样1/O通信线程就可以处理其他的链路，不需要同步等待这个链路可用。
   <br> <br>3. 线程模型的优化:由于JDK的Selector 在Linux等主流操作系统.上通过epoll 实现，
  它没有连接句柄数的限制(只受限于操作系统的最大句柄数或者对单个进程的句柄限制)，
 这意味着一个Selector 线程可以同时处理成千，上万个客户端连接，而且性能不会随着客户端的增加而线性下降。
  因此，它非常适合做高性能、高负载的网络服务器。
  <br> <br> 一个多路复用器Selector可以同时轮询多个Channel，由于JDK使用了epoll()代替 传统的select 实现，
   所以它并没有最大连接句柄1024/2048 的限制。
   这也就意味着只需要一个线程负责Selector的轮询(一个单一的线程便可以处理多个并发的连接)，
   就可以接入成千上万的客户端，这确实是个非常巨大的进步。
   <br> <br>JDK1.7升级了NIO类库，升级后的NIO类库被称为NIO 2.0。 引人注目的是,
  Java正式提供了异步文件I/O操作，  同时提供了与UNIX网络编程事件驱动1/O对应的AIO。

### 7、 pipeline 无锁化串行(链路)
 <br>1. Netty采用了串行无锁化设计，在I/O线程内部进行串行操作，避免多线程竞争导致的性能下降。表面上看，串行化设计似乎CPU利用率不高，并发程度不够。但是，通过调整NIO线程池的线程参数，可以同时启动多个串行化的线程并行运行，
 这种局部无锁化的串行线程设计相比一个队列-多个工作线程模型性能更优。
 <br>实现异步非常重要的一环，无锁化串行的实现 变得可控。同时执行，按顺序取结果。
<br><br> 2. Channel 与 ChannelPipeline
 <br><br>![ChannelPipeline](https://github.com/gaoyuanyuan2/distributed/blob/master/img/23.png) 
  <br>tail  inbound=true, outbound= false  尾进
  <br>head  inbound=false, outbound=true  头出
  <br>参照物:程序，channelNetty异步非阻塞IO框架
  <br>异步发生，都是在线程中响应的
  <br>我要往外面发送东西了Out 请求型的动作
  <br>业务程序来主动触发
  <br>我要开始接收东西了  In  响应型的动作
  <br>Netty来主动触发的，业务程序是被动接收(事件)
###  8、 拆包、粘包
<br>1.TCP粘包、拆包
<br><br>a. 定长，不够空格补全
<br><br>b. 包尾添加特殊分隔符（适合文本）
<br><br>c. 约定协议（将消息分为消息头和消息体）
<br><br>2. 常见用法
<br><br>应用DelimiterBasedFrameDecoder和FixedL engthFrameDecoder进行开发非常简单，在绝大数情况下，
只要将DelimiterBasedFrameDecoder或FixedLengthFrameDecoder添加到对应ChannelPipeline的起始位即可。
###  9、 Jetty轻量级Web容器、Tomcat 重量级Web容器
### 10、WebSocket 
<br>1.连接允许客户端和服务器之间进行全双工通信，以便任一方都可以通过建立的连接将数据推送到另一端。
WebSocket 只需要建立一次连接，就可以一直保持连接状态。这相比于轮询方式的不停建立连接显然效率要大大提高。
<br><br>2.WebSocket的特点
<br>单一的TCP连接，采用全双工模式通信;
<br>对代理、防火墙和路由器透明;
<br>无头部信息、Cookie 和身份验证;
<br>无安全开销;
<br>通过“ping/pong”帧保持链路激活;
<br>服务器可以主动传递消息给客户端，不再需要客户端轮询。
### 11、时序图
1. 服务端
 <br><br>![服务端时序图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/7.png) 
 <br><br>步骤1：创建ServerBootstrap实例。ServerBootstrap是Netty服务端的启动辅助类，它提供了一系列的方法用于设置服务端启动相关的参数。底层通过门面模式对各种能力进行抽象和封装，尽量不需要用户跟过多的底层API打交道，降低用户的开发难度。
我们在创建ServerBootstrap实例时，会惊讶的发现ServerBootstrap只有一个无参的构造函数，作为启动辅助类这让人不可思议，因为它需要与多个其它组件或者类交互。ServerBootstrap构造函数没有参数的根本原因是因为它的参数太多了，而且未来也可能会发生变化，为了解决这个问题，就需要引入Builder模式。《Effective Java》第二版第2条建议遇到多个构造器参数时要考虑用构建器，关于多个参数构造函数的缺点和使用构建器的优点大家可以查阅《Effective Java》,在此不再详述。
 <br><br> 步骤2：设置并绑定Reactor线程池。Netty的Reactor线程池是EventLoopGroup，它实际就是EventLoop的数组。EventLoop的职责是处理所有注册到本线程多路复用器Selector上的Channel，Selector的轮询操作由绑定的EventLoop线程run方法驱动，在一个循环体内循环执行。值得说明的是，EventLoop的职责不仅仅是处理网络I/O事件，用户自定义的Task和定时任务Task也统一由EventLoop负责处理，这样线程模型就实现了统一。从调度层面看，也不存在在EventLoop线程中再启动其它类型的线程用于异步执行其它的任务，这样就避免了多线程并发操作和锁竞争，提升了I/O线程的处理和调度性能。
 <br><br>步骤3：设置并绑定服务端Channel。作为NIO服务端，需要创建ServerSocketChannel,Netty对原生的NIO类库进行了封装，对应实现是NioServerSocketChannel。对于用户而言，不需要关心服务端Channel的底层实现细节和工作原理，只需要指定具体使用哪种服务端Channel即可。因此，Netty的ServerBootstrap方法提供了channel方法用于指定服务端Channel的类型。Netty通过工厂类，利用反射创建NioServerSocketChannel对象。由于服务端监听端口往往只需要在系统启动时才会调用，因此反射对性能的影响并不大。相关代码如下所示：
 <br><br>步骤4：链路建立的时候创建并初始化ChannelPipeline。ChannelPipeline并不是NIO服务端必需的，它本质就是一个负责处理网络事件的职责链，负责管理和执行ChannelHandler。网络事件以事件流的形式在ChannelPipeline中流转，由ChannelPipeline根据ChannelHandler的执行策略调度ChannelHandler的执行。典型的网络事件如下：
  <br>链路注册；
  <br>链路激活；
  <br>链路断开；
  <br>接收到请求消息；
  <br>请求消息接收并处理完毕；
  <br>发送应答消息；
  <br>链路发生异常；
  <br>发生用户自定义事件。
  <br><br>步骤5：初始化ChannelPipeline完成之后，添加并设置ChannelHandler。ChannelHandler是Netty提供给用户定制和扩展的关键接口。利用ChannelHandler用户可以完成大多数的功能定制，例如消息编解码、心跳、安全认证、TSL/SSL认证、流量控制和流量整形等。Netty同时也提供了大量的系统ChannelHandler供用户使用，比较实用的系统ChannelHandler总结如下：
  <br>系统编解码框架-ByteToMessageCodec；
 <br>通用基于长度的半包解码器-LengthFieldBasedFrameDecoder;
 <br>码流日志打印Handler-LoggingHandler；
 <br>SSL安全认证Handler-SslHandler；
 <br>链路空闲检测Handler-IdleStateHandler；
 <br>流量整形Handler-ChannelTrafficShapingHandler;
 <br>Base64编解码-Base64Decoder和Base64Encoder。
<br><br> 步骤6：绑定并启动监听端口。在绑定监听端口之前系统会做一系列的初始化和检测工作，完成之后，会启动监听端口，并将ServerSocketChannel注册到Selector上监听客户端连接
<br><br>步骤7：Selector轮询。由Reactor线程NioEventLoop负责调度和执行Selector轮询操作，选择准备就绪的Channel集合
<br><br>步骤8：当轮询到准备就绪的Channel之后，就由Reactor线程NioEventLoop执行ChannelPipeline的相应方法，最终调度并执行ChannelHandler
<br><br>步骤9：执行Netty系统ChannelHandler和用户添加定制的ChannelHandler。ChannelPipeline根据网络事件的类型，调度并执行ChannelHandler
 <br><br>2. 客户端
 <br><br>![客户端时序图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/7.png) 
<br><br>步骤1：用户线程创建Bootstrap实例
<br><br>步骤2：创建处理客户端连接，I/O读写Reactor线程组NioEventLoopGroup
<br><br>步骤3：创建NioSocketChannel
<br><br>步骤4：创建默认的ChannelHandlerPipeline,用户调度和执行网络事件
<br><br>步骤5：异步发起TCP连接，如果成功将NioSocketChannel注册到多路复用选择器上,监听读操作位,用于数据读取和消息发送,如果失败，注册连接操作位到多路复用选择器，等待连接结果
<br><br>步骤6：注册对应的网络监听状态位到多路复用选择器
<br><br>步骤8：如果连接成功，设置Future结果，发送连接成功事件，触发ChannelHandlerPipeline执行
<br><br>步骤9：由ChannelHandlerPipeline调度和执行系统和用户ChannelHandler
### 12、常用场景
<br>实时计价netty长连接、游戏、心跳检测






















