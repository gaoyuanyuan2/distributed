## Netty
### 1、常见的Reactor线程模型有三种
1. Reactor单线程模型
Reactor单线程模型，指的是所有的I/O操作都在同一个NIO线程上面完成
对于一些小容量应用场景，可以使用单线程模型，但是对于高负载、大并发的应用却不合适，主要原因如下：
1) 一个NIO线程同时处理成百上千的链路，性能上无法支撑。即便NIO线程的CPU负荷达到100%，也无法满足海量消息的编码、解码、读取和发送；
2) 当NIO线程负载过重之后，处理速度将变慢，这会导致大量客户端连接超时，超时之后往往进行重发，这更加重了NIO线程的负载，
最终导致大量消息积压和处理超时，NIO线程会成为系统的性能瓶颈；
3) 可靠性问题。一旦NIO线程意外跑飞，或者进入死循环，会导致整个系统通讯模块不可用，不能接收和处理外部信息，造成节点故障。
![单线程模型](https://github.com/gaoyuanyuan2/distributed/blob/master/img/20.png) 
2. Reactor多线程模型
Reactor多线程模型与单线程模型最大区别就是有一组NIO线程处理I/O操作
在绝大多数场景下，Reactor多线程模型都可以满足性能需求；但是，在极特殊应用场景中，一个NIO线程负责监听和处理所有的客户端连接
可能会存在性能问题。例如百万客户端并发连接，
或者服务端需要对客户端的握手信息进行安全认证，认证本身非常损耗性能。这类场景下，单独一个Acceptor线程可能会存在性能不足问题。
![多线程模型](https://github.com/gaoyuanyuan2/distributed/blob/master/img/21.png) 
3. 主从Reactor多线程模型
服务端用于接收客户端连接的不再是1个单独的NIO线程，而是一个独立的NIO线程池。Acceptor接收到客户端TCP连接请求处理完成后（可能包含接入认证等），
将新创建的SocketChannel注册到I/O线程池（sub reactor线程池）的某个I/O线程上，由它负责SocketChannel的读写和编解码工作。
Acceptor线程池只用于客户端的登录、握手和安全认证，一旦链路建立成功，就将链路注册到后端subReactor线程池的I/O线程上，有I/O线程负责后续的I/O操作。
<font color="#660000">它与多线程模型的主要区别在于其使用一组线程或进程在一个共享的监听套接字上accept连接</font>
![主从多线程模型](https://github.com/gaoyuanyuan2/distributed/blob/master/img/22.png) 

4. 总结

   	 * 零拷贝（直接内存）、线程池、内存池、自定义序列化反序列化

















