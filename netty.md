# Netty

## 优点

1.  采用异步非阻塞的I/O类库，  基于Reactor模式实现，解决了传统同步阻塞1/O模式下一个服务端无法平滑地处理线性增长的客户端的问题。

2.  TCP接收和发送缓冲区使用直接内存代替堆内存，避免了内存复制，提升了I/O读取和写入的性能。（可以回收，一般不回收）

3.  支持通过内存池的方式循环利用ByteBuf,避免了频繁创建和销毁ByteBuf带来的性能损耗。

4.  可配置的I/O线程数、TCP参数等，为不同的用户场景提供定制化的调优参数，满足不同的性能场景。

5.  采用环形数组缓冲区实现无锁化并发编程，代替传统的线程安全容器或者锁。 合理地使用线程安全容器、原子类等， 提升系统的并发处理能力。

7.  关键资源的处理使用单线程串行化的方式，避免多线程并发访问带来的锁竞争和额外的CPU资源消耗问题。

8.  通过引用计数器及时地申请释放不再被引用的对象，细粒度的内存管理降低了GC的频率，减少了频繁GC带来的时延增大和CPU损耗。

## Netty的可定制性主要体现在以下几点

责任链模式: ChannelPipeline 基于责任链模式开发，便于业务逻辑的拦截、定制和扩展。

基于接口的开发:  关键的类库都提供了接口或者抽象类，  如果Netty自身的实现无法满足用户的需求，可以由用户自定义实现相关接口。

提供了大量工厂类，通过重载这些工厂类可以按需创建出用户实现的对象。

提供了大量的系统参数供用户按需设置，增强系统的场景定制性。

## Netty高性能之道

异步非阻塞通信


高效的Reactor线程模型


无锁化的串行设计


高效的并发编程


高性能的序列化框架


零拷贝


内存池


灵活的TCP参数配置能力

## 为什么选Netty

用Netty来构建高性能的异步通信能力。


1.  API使用简单，开发门槛低;


2.  功能强大，预置了多种编解码功能，支持多种主流协议;


3.  定制能力强，可以通过ChannelHandler对通信框架进行灵活地扩展;


4.  性能高，通过与其他业界主流的NIO框架对比，Netty 的综合性能最优;


5.  成熟、稳定，Netty 修复了已经发现的所有JDK NIO BUG,  业务开发人员不需要再为NIO的BUG而烦恼;


6.  社区活跃，版本迭代周期短，发现的BUG可以被及时修复，  同时，更多的新功能会加入;


7.  经历了大规模的商业应用考验，质量得到验证。Netty 在互联网、大数据、网络


8.  游戏、企业应用、电信软件等众多行业已经得到了成功商用，证明它已经完全能够满足不同行业的商业应用了。


9.  正是因为这些优点，Netty逐渐成为了JavaNIO编程的首选框架。

## 常见的Reactor线程模型有三种

1. Reactor单线程模型

Reactor单线程模型，指的是所有的I/O操作都在同一个NIO线程上面完成对于一些小容量应用场景，可以使用单线程模型，
但是对于高负载、大并发的应用却不合适，主要原因如下：


1) 一个NIO线程同时处理成百上千的链路，性能上无法支撑。即便NIO线程的CPU负荷达到100%，也无法满足海量消息的编码、解码、读取和发送；


2) 当NIO线程负载过重之后，处理速度将变慢，这会导致大量客户端连接超时，超时之后往往进行重发，这更加重了NIO线程的负载，
最终导致大量消息积压和处理超时，NIO线程会成为系统的性能瓶颈；


3) 可靠性问题。一旦NIO线程意外跑飞，或者进入死循环，会导致整个系统通讯模块不可用，不能接收和处理外部信息，造成节点故障。


![单线程模型](/img/20.png) 


2. Reactor多线程模型

Reactor多线程模型与单线程模型最大区别就是有一组NIO线程处理I/O操作
在绝大多数场景下，Reactor多线程模型都可以满足性能需求；但是，在极特殊应用场景中，一个NIO线程负责监听和处理所有的客户端连接
可能会存在性能问题。例如百万客户端并发连接，
或者服务端需要对客户端的握手信息进行安全认证，认证本身非常损耗性能。这类场景下，单独一个Acceptor线程可能会存在性能不足问题。


![多线程模型](/img/21.png) 


3. 主从Reactor多线程模型（效率最高）

服务端用于接收客户端连接的不再是1个单独的NIO线程，而是一个独立的NIO线程池。Acceptor接收到客户端TCP连接请求处理完成后（可能包含接入认证等），
将新创建的SocketChannel注册到I/O线程池（sub reactor线程池）的某个I/O线程上，由它负责SocketChannel的读写和编解码工作。
Acceptor线程池只用于客户端的登录、握手和安全认证，一旦链路建立成功，就将链路注册到后端subReactor线程池的I/O线程上，有I/O线程负责后续的I/O操作。

*它与多线程模型的主要区别在于其使用一组线程或进程在一个共享的监听套接字上accept连接*


![主从多线程模型](/img/22.png) 

Boss 调度资源的总管家(Dispatcher)接待大厅，用来负责轮询Selector、事件的管理(SelectionKey):while(true)轮询

Worker 子线程一个一个地去执行Handler

## nio 

Selector会不断地轮询注册在其上的Channel,如果某个Channel上面发生读或者写事件，这个Channel就处于就绪状态，会被Selector轮询出来，然后通过SelectionKey可以获取就绪Channel的集合，进行后续的I/O操作。

## 零拷贝

1. 零拷贝:是指计算机操作的过程中，CPU不需要为数据在内存之间的拷贝消耗资源。而它通常是指计算机在网络上发送文件时，
不需要将文件内容拷贝到用户空间（JVM）而直接在内核空间（Kernel Space）中传输到网络的方式。

##  源码 

只要是方法后面加了个0的，都是实现类的方法，不是接口在Netty中提供了非常丰富的工具类。

##  对比

同步:相对于IO操作而言的，在同一时间，只能完成一个操作(JDK NIO)


异步:相对于I0操作而言的，在同一时间，  同时完成多个操作(JDK AIO)


阻塞:相对于数据而言，判断数据有没有准备好，如果没有准备好，停住不前（BIO  accept）


非阻塞:不管数据有没有准备好，都会给一个反馈（NIO） 

Socket又称套接字：服务端监听、客户端请求服务器，服务器确认，客户端确认，进行通信

1. BIO 同步阻塞

```java
Socket client = server.accept();//等待客户端连接，阻塞方法
```


2. NIO 同步非阻塞


3. AIO 异步非阻塞

NIO2.0的异步套接字通道是真正的异步非阻塞I/O,对应于UNIX网络编程中的事件驱动I/O(AIO)。
它不需要通过多路复用器(Selector)对注册的通道进行轮询操作即可实现异步读写，从而简化了NIO的编程模型。
通过java. util.concurrent.Future类来表示异步操作的结果;
在执行异步操作的时候传入一个java.nio.channels。
CompletionHandler接口的实现类作为操作完成的回调。 
Netty   NIO框架(加了线程池ThreadPool )
异步非阻塞(基于NI0来实现)

## 执行流程

1. Server:


步骤一:打开ServerSocketChannel,用于监听客户端的连接，它是所有客户端连接的父管道

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
步骤十一:将POJO对象encode成ByteBuffer, 调用SocketChannel的异步write 接口，将消息异步发送给客户端

2. Client


步骤一:打开SocketChannel,绑定客户端本地地址(可选，默认系统会随机分配一个可用的本地地址)

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


步骤五:向Reactor线程的多路复用器注册OP_CONNECT状态位，监听服务端的TCPACK应答


步骤六:创建Reactor线程，创建多路复用器并启动线程


步骤七:多路复用器在线程run方法的无限循环体内轮询准备就绪的Key


步骤八:接收connect事件进行处理


步骤九:判断连接结果，如果连接成功，注册读事件到多路复用器


步骤十:注册读事件到多路复用器


步骤十一:异步读客户端请求消息到缓冲区


步骤十二:对ByteBuffer进行编解码，如果有半包消息接收缓冲区Reset, 继续读取后续的报文，将解码成功的消息封装成Task,投递到业务线程池中，进行业务逻辑编排。


步骤十三:将POJO对象encode成ByteBuffer, 调用SocketChannel的异步write 接口,将消息异步发送给客户端。


## NIO编程的优点

1. 客户端发起的连接操作是异步的，可以通过在多路复用器注册OP_CONNECT等待后续结果，不需要像之前的客户端那样被同步阻塞。


2. SocketChannel 的读写操作都是异步的，如果没有可读写的数据它不会同步等待，直接返回，
这样1/O通信线程就可以处理其他的链路，不需要同步等待这个链路可用。


3. 线程模型的优化:由于JDK的Selector 在Linux等主流操作系统.上通过epoll 实现，
它没有连接句柄数的限制(只受限于操作系统的最大句柄数或者对单个进程的句柄限制)，
这意味着一个Selector 线程可以同时处理成千，上万个客户端连接，而且性能不会随着客户端的增加而线性下降。
因此，它非常适合做高性能、高负载的网络服务器。
一个多路复用器Selector可以同时轮询多个Channel，由于JDK使用了epoll()代替 传统的select 实现，
所以它并没有最大连接句柄1024/2048 的限制。

这也就意味着只需要一个线程负责Selector的轮询(一个单一的线程便可以处理多个并发的连接)，
就可以接入成千上万的客户端，这确实是个非常巨大的进步。

JDK1.7升级了NIO类库，升级后的NIO类库被称为NIO 2.0。 引人注目的是,
Java正式提供了异步文件I/O操作，  同时提供了与UNIX网络编程事件驱动1/O对应的AIO。

4. 非常丰富的工具类

## pipeline 无锁化串行(链路)

1. Netty采用了串行无锁化设计，在I/O线程内部进行串行操作，避免多线程竞争导致的性能下降。 表面上看，串行化设计似乎CPU利用率不高，并发程度不够。但是，通过调整NIO线程池的线程参数，可以同时启动多个串行化的线程并行运行，
这种局部无锁化的串行线程设计相比一个队列多个工作线程模型性能更优。
实现异步非常重要的一环，无锁化串行的每个线程执行的顺序变得可控。同时执行，按顺序取结果。


2. Channel 与 ChannelPipeline

![ChannelPipeline](/img/23.png) 

* 不是队列

参照物：channel

tail  inbound=true, outbound= false  尾进 （接收）Netty来主动触发的，业务程序是被动接收(事件)

head  inbound=false, outbound=true  头出 （发出） 业务程序来主动触发


参照物:程序，channelNetty异步非阻塞IO框架

异步发生，都是在线程中响应的

我要往外面发送东西了Out 请求型的动作

业务程序来主动触发

我要开始接收东西了  In  响应型的动作

Netty来主动触发的，业务程序是被动接收(事件)

## 拆包、粘包

1.TCP底层 粘包、拆包


a. 定长，不够空格补全


b. 包尾添加特殊分隔符（适合文本）


c. 约定协议（将消息分为消息头和消息体）


2. 常见用法

应用DelimiterBasedFrameDecoder和FixedLengthFrameDecoder进行开发非常简单，在绝大数情况下，

只要将DelimiterBasedFrameDecoder或FixedLengthFrameDecoder添加到对应ChannelPipeline的起始位即可。

## HTTP多协议开发和应用

在Java领域，最常用的HTTP协议栈就是基于Servlet规范的Tomcat等Web容器，
由于Netty天生是异步事件驱动的架构，因此基于NIO TCP协议栈开发的HTTP协议栈也是异步非阻塞的。
Netty的HTTP协议栈无论在性能还是可靠性上，都表现优异，  非常适合在非Web容器的场景下应用，  相比于传统的Tomcat、Jetty等Web容器，它更加轻量和小巧，  灵活性和定制性也更好。
在网络安全日益严峻的今天，重量级的Web容器由于功能繁杂， 
会存在很多安全漏洞，典型的如Tomcat。如果你的客户是安全敏感型的，这意味着你需要为Web容器做很多安全加固工作去修补这些漏洞，
然而你并没有使用到这些功能，这会带来开发和维护成本的增加。在这种场景下，一个更加轻量级的HTTP协议栈是个更好的选择。


## WebSocket 协议开发

HTTP协议的主要弊端总结如下。


1.  HTTP协议为半双工协议。半双工协议指数据可以在客户端和服务端两个方向上传输，但是不能同时传输。它意味着在同一时刻，只有一个方向上的数据传送;


2.   HTTP消息冗长而繁琐。HTTP消息包含消息头、消息体、换行符等，通常情况下采用文本方式传输，相比于其他的二进制通信协议，冗长而繁琐;


3.  针对服务器推送的黑客攻击。例如长时间轮询。


现在，很多网站为了实现消息推送，所用的技术都是轮询。轮询是在特定的的时间间隔(如每1秒)，由浏览器对服务器发出HTTP request,然后由服务器返回最新的数据给客户端浏览器。
这种传统的模式具有很明显的缺点，即浏览器需要不断地向服务器发出请求，然而HTTP request的Header是非常冗长的，里面包含的可用数据比例可能非常低，这会占用很多的带宽和服务器资源。
比较新的一种轮询技术是Comet,  使用了AJAX。  这种技术虽然可达到双向通信，但依然需要发出请求，而且在Comet中，  普遍采用了长连接，这也会大量消耗服务器带宽和资源。
为了解决HTTP协议效率低下的问题，HTML5定义了WebSocket 协议，能更好地节省服务器资源和带宽并达到实时通信。
WebSocket 基于TCP双向全双工进行消息传递，  在同一时刻，既可以发送消息，  也可以接收消息，相比HTTP的半双工协议，  性能得到很大提升。


下面总结一下WebSocket的特点。


1.  单一的TCP连接，采用全双工模式通信;


2.  对代理、防火墙和路由器透明;


3.  无头部信息、Cookie 和身份验证;


4.  无安全开销;


5.  通过“ping/pong”帧保持链路激活;


6.  服务器可以主动传递消息给客户端，不再需要客户端轮询。


1.连接允许客户端和服务器之间进行全双工通信，以便任一方都可以通过建立的连接将数据推送到另一端。
WebSocket 只需要建立一次连接，就可以一直保持连接状态。这相比于轮询方式的不停建立连接显然效率要大大提高。


2.WebSocket的特点

单一的TCP连接，采用全双工模式通信;

对代理、防火墙和路由器透明;

无头部信息、Cookie 和身份验证;

无安全开销;

通过“ping/pong”帧保持链路激活;

服务器可以主动传递消息给客户端，不再需要客户端轮询。


## 时序图

1. 服务端

![服务端时序图](/img/7.png) 


步骤1：创建ServerBootstrap实例。ServerBootstrap是Netty服务端的启动辅助类，它提供了一系列的方法用于设置服务端启动相关的参数。底层通过门面模式对各种能力进行抽象和封装，尽量不需要用户跟过多的底层API打交道，降低用户的开发难度。
我们在创建ServerBootstrap实例时，会惊讶的发现ServerBootstrap只有一个无参的构造函数，作为启动辅助类这让人不可思议，因为它需要与多个其它组件或者类交互。ServerBootstrap构造函数没有参数的根本原因是因为它的参数太多了，
而且未来也可能会发生变化，为了解决这个问题，就需要引入Builder模式。《Effective Java》第二版第2条建议遇到多个构造器参数时要考虑用构建器。


步骤2：设置并绑定Reactor线程池。Netty的Reactor线程池是EventLoopGroup，它实际就是EventLoop的数组。
EventLoop的职责是处理所有注册到本线程多路复用器Selector上的Channel，Selector的轮询操作由绑定的EventLoop线程run方法驱动，
在一个循环体内循环执行。值得说明的是，EventLoop的职责不仅仅是处理网络I/O事件，用户自定义的Task和定时任务Task也统一由EventLoop负责处理，
这样线程模型就实现了统一。从调度层面看，也不存在在EventLoop线程中再启动其它类型的线程用于异步执行其它的任务，这样就避免了多线程并发操作和锁竞争，提升了I/O线程的处理和调度性能。


步骤3：设置并绑定服务端Channel。作为NIO服务端，需要创建ServerSocketChannel,Netty对原生的NIO类库进行了封装，对应实现是NioServerSocketChannel。对于用户而言，不需要关心服务端Channel的底层实现细节和工作原理，
只需要指定具体使用哪种服务端Channel即可。因此，Netty的ServerBootstrap方法提供了channel方法用于指定服务端Channel的类型。
Netty通过工厂类，利用反射创建NioServerSocketChannel对象。由于服务端监听端口往往只需要在系统启动时才会调用，因此反射对性能的影响并不大。


步骤4：链路建立的时候创建并初始化ChannelPipeline。ChannelPipeline并不是NIO服务端必需的，它本质就是一个负责处理网络事件的职责链，
负责管理和执行ChannelHandler。网络事件以事件流的形式在ChannelPipeline中流转，由ChannelPipeline根据ChannelHandler的执行策略调度ChannelHandler的执行。典型的网络事件如下：

链路注册；

链路激活；

链路断开；

接收到请求消息；

请求消息接收并处理完毕；

发送应答消息；

链路发生异常；

发生用户自定义事件。


步骤5：初始化ChannelPipeline完成之后，添加并设置ChannelHandler。ChannelHandler是Netty提供给用户定制和扩展的关键接口。
利用ChannelHandler用户可以完成大多数的功能定制，
例如消息编解码、心跳、安全认证、TSL/SSL认证、流量控制和流量整形等。Netty同时也提供了大量的系统ChannelHandler供用户使用，比较实用的系统ChannelHandler总结如下：

系统编解码框架-ByteToMessageCodec；

通用基于长度的半包解码器-LengthFieldBasedFrameDecoder;

码流日志打印Handler-LoggingHandler；

SSL安全认证Handler-SslHandler；

链路空闲检测Handler-IdleStateHandler；

流量整形Handler-ChannelTrafficShapingHandler;

Base64编解码-Base64Decoder和Base64Encoder。


步骤6：绑定并启动监听端口。在绑定监听端口之前系统会做一系列的初始化和检测工作，完成之后，会启动监听端口，并将ServerSocketChannel注册到Selector上监听客户端连接


步骤7：Selector轮询。由Reactor线程NioEventLoop负责调度和执行Selector轮询操作，选择准备就绪的Channel集合


步骤8：当轮询到准备就绪的Channel之后，就由Reactor线程NioEventLoop执行ChannelPipeline的相应方法，最终调度并执行ChannelHandler


步骤9：执行Netty系统ChannelHandler和用户添加定制的ChannelHandler。ChannelPipeline根据网络事件的类型，调度并执行ChannelHandler

2. 客户端

![客户端时序图](/img/7.png) 


步骤1：用户线程创建Bootstrap实例


步骤2：创建处理客户端连接，I/O读写Reactor线程组NioEventLoopGroup


步骤3：创建NioSocketChannel


步骤4：创建默认的ChannelHandlerPipeline,用户调度和执行网络事件


步骤5：异步发起TCP连接，如果成功将NioSocketChannel注册到多路复用选择器上,监听读操作位,用于数据读取和消息发送,如果失败，注册连接操作位到多路复用选择器，等待连接结果


步骤6：注册对应的网络监听状态位到多路复用选择器


步骤8：如果连接成功，设置Future结果，发送连接成功事件，触发ChannelHandlerPipeline执行


步骤9：由ChannelHandlerPipeline调度和执行系统和用户ChannelHandler

## 常用场景

实时计价netty长连接、游戏、心跳检测


## 协议栈功能描述


Netty协议栈承载了业务内部各模块之间的消息交互和服务调用，它的主要功能如下。


1.  基于Netty的NIO通信框架，提供高性能的异步通信能力;


2.  提供消息的编解码框架，可以实现POJO的序列化和反序列化;


3.  提供基于IP地址的白名单接入认证机制;(4)链路的有效性校验机制;


4.  链路的断连重连机制。


## ByteBuf

ByteBuffer缺点 


1.  ByteBuffer长度固定，一旦分配完成，它的容量不能动态扩展和收缩，当需要编码的POJO对象大于ByteBuffer的容量时，会发生索引越界异常;


2.  ByteBuffer 只有一个标识位置的指针position,读写的时候需要手工调用flip()和rewind()等，使用者必须小心谨慎地处理这些API，否则很容易导致程序处理失败;


3.  ByteBuffer 的API 功能有限，一些高级和实用的特性它不支持，需要使用者自己编程实现。

为了弥补这些不足，Netty 提供了自己的ByteBuffer实现一ByteBuf


从内存分配的角度看，ByteBuf 可以分为两类。

1.  堆内存(HeapByteBuf) 字节缓冲区;  特点是内存的分配和回收速度快，可以被JVM自动回收;  缺点就是如果进行Socket的I/O 读写，  需要额外做一次内存复制，将堆内存对应的缓冲区复制到内核Channel中，  
性能会有一'定程度的下降。


2.  直接内存( DirectByteBuf) 字节缓冲区:非堆内存，它在堆外进行内存分配，相比于堆内存，它的分配和回收速度会慢一些，但是将它写入或者从Socket 
Channel中读取时，由于少了一次内存复制，速度比堆内存快。

##  Channel和Unsafe

Channel的工作原理


Channel是Netty抽象出来的网络1/O读写相关的接口，为什么不使用JDK NIO原生的Channel而要另起炉灶呢，主要原因如下。


1. JDK的SocketChannel和ServerSocketChannel没有统一的Channel接口供业务开发者使用，对于用户而言，没有统一的操作视图，使用起来并不方便。


2. JDK的SocketChannel和ServerSocketChannel的主要职责就是网络1/O操作，由于它们是SPI类接口，由具体的虚拟机厂家来提供，所以通过继承SPI功能类来扩展其功能的难度很大;
直接实现ServerSocketChannel和SocketChannel抽象类，其工作量和重新开发一个新的Channel功能类是差不多的。


3.  Netty的Channel需要能够跟Netty的整体架构融合在一起，例如I/O模型、基于ChannelPipeline的定制模型，以及基于元数据描述配置化的TCP参数等，这些JDK的SocketChannel
和ServerSocketChannel都没有提供，需要重新封装。


4.  自定义的Channel, 功能实现更加灵活。

基于上述4个原因，Netty 重新设计了Channel 接口，并且给予了很多不同的实现。它的设计原理比较简单，但是功能却比较繁杂，主要的设计理念如下。


1.  在Channel接口层，采用Facade模式进行统-封装，将网络l/O操作、网络I/O相关联的其他操作封装起来，统一对外提供。

2.  Channel 接口的定义尽量大而全，为SocketChannel 和ServerSocketChannel提供统一的视图，由不同子类实现不同的功能，公共功能在抽象父类中实现，最大程度地实现功能和接口的重用。

3.  具体实现采用聚合而非包含的方式,将相关的功能类聚合在Channel中，由Channel统一负责分配和调度，功能实现更加灵活。


Unsafe功能说明

Unsafe接口实际上是Channel接口的辅助接口，它不应该被用户代码直接调用。实际的I/O读写操作都是由Unsafe接口负责完成的。


## ChannelPipeline和ChannelHandler

ChannelPipeline是ChannelHandler的容器，它负责ChannelHandler的管理和事件拦截与调度。

ChannelPipeline通过ChannelHandler 接口来实现事件的拦截和处理，

由于ChannelHandler中的事件种类繁多，不同的ChannelHandler可能只需要关心其中的某一个或者几个事件，所以，

通常ChannelHandler只需要继承ChannelHandlerAdapter 类覆盖自已关心的方法即可。



ChannelPipeline的主要特性

ChannelPipeline支持运行态动态的添加或者删除ChannelHandler,在某些场景下这个特性非常实用。例如当业务高峰期需要对系统做拥塞保护时， 

就可以根据当前的系统时间进行判断，如果处于业务高峰期，则动态地将系统拥塞保护ChannelHandler添加到当前的ChannelPipeline中，当高峰期过去之后，就可以动态删除拥塞保护ChannelHandler了。

ChannelPipeline是线程安全的,这意味着N个业务线程可以并发地操作ChannelPipeline而不存在多线程并发问题。但是，  

* ChannelHandler却不是线程安全的，这意味着尽管ChannelPipeline是线程安全的，但是用户仍然需要自己保证ChannelHandler的线程安全。


ChannelHandler功能说明

ChannelHandler类似于Servlet 的Filter 过滤器，负责对1/O 事件或者1/O操作进行拦截和处理，它可以选择性地拦截和处理自己感兴趣的事件，也可以透传和终止事件的传递。

基于ChannelHandler接口，用户可以方便地进行业务逻辑定制，例如打印日志、统一封装异常信息、性能统计和消息编解码等。

ChannelHandler支持注解，目前支持的注解有两种。

Sharable:多个ChannelPipeline共用同一个ChannelHandler; 

Skip:被Skip注解的方法不会被调用，直接被忽略。

ChannelHandlerAdapter功能说明

对于大多数的ChannelHandler会选择性地拦截和处理某个或者某些事件，其他的事件会忽略，由下一个ChannelHandler进行拦截和处理。 
这就会导致一个问题:用户ChannelHandler必须要实现ChannelHandler的所有接口，包括它不关心的那些事件处理接1，这会导致用户代码的冗余和臃肿，代码的可维护性也会变差。
为了解决这个问题，Netty 提供了ChannelHandlerAdapter基类，它的所有接口实现都是事件透传，如果用户ChannelHandler关心某个事件， 
只需要覆盖ChannelHandlerAdapter对应的方法即可，对于不关心的，可以直接继承使用父类的方法，这样子类的代码就会非常简洁和清晰。前面几章样例代码中， 
我们的ChannelHandler都是直接继承自ChannelHandlerAdapter,开发起来非常简单和高效。


ByteToMessageDecoder功能说明

利用NIO进行网络编程时，往往需要将读取到的字节数组或者字节缓冲区解码为业务可以使用的POJO对象。为了方便业务将ByteBuf解码成业务POJO对象，Netty 提供了ByteToMessageDecoder抽象工具解码类。
用户的解码器继承ByteToMessageDecoder,只需要实现`void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out)`

抽象方法即可完成ByteBuf到POJO对象的解码。

由于ByteToMessageDecoder并没有考虑TCP粘包和组包等场景，读半包需要用户解码器自己负责处理。正因为如此，对于大多数场景不会直接继承ByteToMessageDecoder,而是继承另外一些更高级的解码器来屏蔽半包的处理。


MessageToMessageDecoder功能说明

MessageToMessageDecoder实际上是Netty 的二次解码器， 它的职责是将一个对象二次解码为其他对象。

为什么称它为二次解码器呢?我们知道，从SocketChannel 读取到的TCP数据报是ByteBuffer,实际就是字节数组，我们首先需要将ByteBuffer缓冲区中的数据报读取出来，并将其解码为Java对象;然后对Java对象根据某些规则做二次解码，将其解码为另一个POJO对象。因为MessageToMessageDecoder在ByteToMessageDecoder之后，所以称之为二次解码器。
二次解码器在实际的商业项目中非常有用，以HTTP+XML协议栈为例，第一次解码往往是将字节数组解码成HttpRequest对象，然后对HttpRequest消息中的消息体字符串进行二次解码，将XML格式的字符串解码为POJO对象，这就用到了二次解码器。类似这样的场景还有很多，不再  枚举。
事实上，做一个超级复杂的解码器将多个解码器组合成一个大而全的MessageToMessageDecoder解码器似乎也能解决多次解码的问题，但是采用这种方式的代码可维护性会非常差。例如，如果我们打算在HTTP+XML协议栈中增加一个打印码流的功能，即首次解码获取HttpRequest对象之后打印XML格式的码流。
如果采用多个解码器组合，在中间插入一个打印消息体的Handler即可，不需要修改原有的代码;如果做一个大而全的解码器，就需要在解码的方法中增加打印码流的代码，可扩展性和可维护性都会变差。
用户的解码器只需要实现`void decode(ChannelHandlerContext ctx, I msg, List<Object> out)` 
抽象方法即可，由于它是将一个POJO解码为另一个POJO，所以一-般不会涉及到半包的处理，相对于ByteToMessageDecoder 更加简单些。


LengthFieldBasedFrameDecoder功能说明

在编解码章节我们讲过TCP的粘包导致解码的时候需要考虑如何处理半包的问题


如何区分一个整包消息，通常有如下4种做法。

固定长度，例如每120个字节代表一个整包消息，不足的前面补零。解码器在处理这类定常消息的时候比较简单，每次读到指定长度的字节后再进行解码。

通过回车换行符区分消息，例如FTP协议。这类区分消息的方式多用于文本协议。

通过分隔符区分整包消息。

通过指定长度来标识整包消息。


如果消息是通过长度进行区分的，LengthFieldBasedFrameDecoder 都可以自动处理粘包和半包问题，只需要传入正确的参数，即可轻松搞定“读半包”问题。


MessageToByteEncoder功能说明

MessageToByteEncoder负责将POJO对象编码成ByteBuf,用户的编码器继承MessageToByteEncoder,实现`void encode(ChannelHandlerContext ctx, I msg, ByteBuf out)`接口接口


MessageToMessageEncoder功能说明

将一个POJO对象编码成另一个对象，以HTTP+XML协议为例，它的一种实现方式是:先将POJO对象编码成XML字符串， 
再将字符串编码为HTTP请求或者应答消息。对于复杂协议，往往需要经历多次编码，为了便于功能扩展，可以通过多个编码器组合来实现相关功能。


LengthFieldPrepender功能说明

如果协议中的第-一个字段为长度字段，Netty 提供了LengthFieldPrepender 编码器，它可以计算当前待发送消息的二进制字节长度，将该长度添加到ByteBuf 的缓冲区头中。
相对于ByteBuf 和Channel, ChannelHandler 的类继承关系稍微简单些，但是它的子类非常多。由于ChannelHandler是Netty框架和用户代码的主要扩展和定制点，所以它的子类种类繁多、功能各异，系统ChannelHandler主要分类如下。
ChannelPipeline的系统ChannelHandler,用于I/O操作和对事件进行预处理，对于用户不可见，这类ChannelHandler主要包括HeadHandler和TailHandler;
编解码ChannelHandler, 包括ByteToMessageCode. MessageToMessageDecoder等，这些编解码类本身又包含多种子类。
其他系统功能性ChannelHandler,包括流量整型Handler、读写超时Handler、日志Handler等。


Byte ToMessageDecoder源码分析 顾名思义，ByteToMessageDecoder 解码器用于将ByteBuf解码成POJO对象，下面一起看它的实现。


MessageToMessageDecoder负责将一个POJO对象解码成另一个POJO对象。


LengthFieldBasedFrameDecoder基于消息长度的半包解码器。


MessageToByteEncoder负责将用户的POJO对象编码成ByteBuf, 以便通过网络进行传输。


MessageToMessageEncoder源码分析 Message ToMessageEncoder负责将-一个POJO 对象编码成另一个POJO对象，例如将XML Document对象编码成XML格式的字符串。


LengthFieldPrepender负责在待发送的ByteBuf消息头中增加一个长度字段来标识消息的长度，  它简化了用户的编码器开发，使用户不需要额外去设置这个长度字段。


## EventLoop和EventLoopGroup

Netty的多线程编程最佳实践如下。


(1)创建两个NioEventLoopGroup, 用于逻辑隔离NIO Acceptor和NIO I/O线程。


(2)尽量不要在ChannelHandler中启动用户线程(解码后用于将POJO消息派发到后端业务线程的除外)。


(3)解码要放在NIO线程调用的解码Handler中进行，不要切换到用户线程中完成消息的解码。


(4)如果业务逻辑操作非常简单，没有复杂的业务逻辑计算，没有可能会导致线程被阻塞的磁盘操作、数据库操作、网路操作等，可以直接在NIO线程上完成业务逻辑编排，不需要切换到用户线程。


(5)如果业务逻辑处理复杂，不要在NIO线程上完成，建议将解码后的POJO消息封装成Task,派发到业务线程池中由业务线程执行，以保证NIO线程尽快被释放，处理其他的1/O操作。

推荐的线程数量计算公式有以下两种。


公式一:线程数量= (线程总时间/瓶颈资源时间) x瓶颈资源的线程并行数。


公式二: QPS= 1000/线程总时间x线程数。


由于用户场景的不同，对于一些复杂的系统，实际上很难计算出最优线程配置，只能是根据测试数据和用户场景，结合公式给出一个相对合理的范围，然后对范围内的数据进行性能测试，选择相对最优值。
Netty的NioEventLoop并不是一个纯粹的I/O线程，兼顾处理以下两类任务。


它除了负责I/O的读写之外， 还系统Task:通过调用NioEventLoop 的execute(Runnable task)方法实现，Netty 有很多系统Task，创建它们的主要原因是:当I/O线程和用户线程同时操作网络资源时，
为了防止并发操作导致的锁竞争，将用户线程的操作封装成Task 放入消息队列中，由1/O线程负责执行，这样就实现了局部无锁化。


定时任务:通过调用NioEventLoop 的`schedule( Runnable command, long delay,TimeUnit unit)`方法实现。

正是因为NioEventLoop具备多种职责，所以它的实现比较特殊，它并不是个简单的Runnable。
它实现了EventLoop 接口、EventExecutorGroup接口和ScheduledExecutorService接口，正是因为这种设计，导致NioEventLoop和其父类功能实现非常复杂。下面我们就重点分析它的源码实现，理解它的设计原理。


## Future和Promise

ChannelFuture有两种状态; uncompleted 和completed。当开始一个l/O操作时， 一个新的ChannelFuture被创建，此时它处于uncompleted状态一非失败、 非成功、非取消，
因为I/O操作此时还没有完成。一旦1/O操作完成,ChannelFuture将会被设置成completed,它的结果有如”下三种可能。

操作成功;

操作失败;

操作被取消。


当I/O操作完成之后，I/O线程会回调ChannelFuture 中GenericFutureListener 的operationComplete方法，  
并把ChannelFuture对象当作方法的入参。如果用户需要做上下文相关的操作，  需要将，上下文信息保存到对应的ChannelFuture中。
推荐通过GenericFutureListener代替ChannelFuture的get等方法的原因是:当我们进行异步I/O操作时，完成的时间是无法预测的
，如果不设置超时时间，它会导致调用线程长时间被阻塞，  甚至挂死。而设置超时时间，  时间又无法精确预测。利用异步通知机制回调GenericFutureListener是最佳的解决方案，它的性能最优。


需要注意的是:  不要在ChannelHandler中调用ChannelFuture 的await()
方法，这会导致死锁。原因是发起I/O操作之后，由I/O线程负责异步通知发起1/O操作的用户线程，如果I/O线程和用户线程是同一个线程，
就会导致1/O线程等待自己通知操作完成，这就导致了死锁，这跟经典的两个线程互等待死锁不同，  属于自己把自已挂死。
Promise是可写的Future, Future 自身并没有写操作相关的接口，Netty 通过Promise对Future进行扩展，用于设置I/O操作的结果。

## 设计模式

装饰器 模板方法 工厂 反应堆 责任链

## 实战

### 1. 速联星空GPS长连接服务器实时存入轨迹数据

二进制、自定义协议、特殊符号拆分


消息体、消息体、验证、位操作


二进制转对象（编解码器）

过程：连接、读、写...

**注意：耗时操作（http请求、数据库连接、io操作）不能放在io线程，要另起线程，否则会阻塞**

优点：通过心跳维持的连接不会经常断开，即可实现即时的通信，而且可自定义头，减小流量的耗用
















  



























