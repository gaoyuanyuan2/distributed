## 一、分布式架构概述
### 1、分布式
<br>一个业务拆分成多个子系统，部署在不同的服务器上（相互调用）。
### 2、集群
<br>同一个业务，部署在多个服务器上。高可用。
### 3、大型网站
<br> 访问量（tps每秒传输的事物处理个数、qps每秒查询率）
<br> 数据量（存储数据量）
### 4、读写分离
### 5、搜索引擎
### 6、缓存、限流、降级、分库、表拆分（500w~1000w）
### 7、解决session跨域
<br> cookie 存sessionid，服务端Tomcat用ConcuurentMap存储
<br> session sticky
<br> session replication 
<br> session 集中存储 db、缓存服务器
<br> cookie （主流） access token（userid/token/timestamp）
<br> soa 架构和微服务架构
## 二、分布式通信协议
### 1、网络协议：TCP/IP 和UDP/IP
<br> TCP五层:物理层（物理介质）、数据链路层（硬件接口）、网络层（IP ICMP/IGMP）、传输层（TCP/UDP）、应用层（应用程序）
<br><br> 3次握手协议（确认）。伪造IP发送http请求，不给回复，导致连接队列栈满，
DoS攻击是指故意的攻击网络协议实现的缺陷或直接通过野蛮手段残忍地耗尽被攻击对象的资源。
<br>你爱我吗？我爱你，我知道了。
<br><br> 4次挥手协议。
<br>你爱我吗？我收到了。我爱你。我收到了。
<br><br> 单工，半双工 对讲 ，全双工（TCP）吵架
<br><br> UDP 不可靠，不建立连接，视频，性能好。
### 2、NIO（同步非阻塞）主动问，AIO（异步非阻塞） 被动通知。
<br> 滑动窗口协议（缓冲区）：传输的每个部分被分配唯一的连续序列号，接收方使用数字并以正确的顺序放置接收到的数据包，丢弃重复的数据包并识别丢失的数据。
### 3、Multicase（组播）
<br>基于TCP
<br>叫一群“美女”，会有一组女生回头。
## 三、分布式通信-序列化
### 1、简介
<br><br>java序列化：数据大，效率低，不能跨语言。
<br><br>通用性、安全性、优化性能。
<br><br>UID 版本号：保证序列化和反序列化是同一个。
<br><br>序列化不保存静态变量的状态。Transient 不参与序列化。父类没实现序列化接口，子类实现了，父类属性不生效。
同一个对象存储两次不会叠加，只是引用，如果属性有改变，会对改变的值存储。
<br><br>XML编码 易懂，跨语言。
<br><br>HTTP RESTful 跨语言，无状态，数据大。
<br><br>MessagePack 开源序列化框架
<br><br>Protocol Buffers:压缩字节数小、序列化快、缓存机制。
<br><br>序列化实现深克隆
<br>浅克隆：复制对象，不复制对象引用。
### 2、主流
<br>JSON/Hessian(2)/xml/protobuf/kryo/MsgPack/FST/thrift/protostuff/Avro






