#  分布式架构概述

![](/img/25.jpg) 

## 1、分布式 

单机计算机的架构->分布式计算机架构

## 2、集群

同一个业务，部署在多个服务器上。高可用。

## 3、大型网站

 访问量（tps每秒传输的事物处理个数、qps每秒查询率）

数据量（存储数据量）

## 4、读写分离

## 5、搜索引擎

## 6、缓存、限流、降级、分库、表拆分（500w~1000w）

## 7、解决session跨域

cookie 存sessionid，服务端Tomcat用ConcuurentMap存储

session sticky （请求落在同一个服务器上）

session replication  （复制）

session 集中存储 db、缓存服务器

cookie （主流）保存在客户端  access token（userid/token/timestamp）加密，拦截器解析token， 判断有效期，用户是否存在

好处：无状态，无需存储在服务端。

soa 架构和微服务架构

#  分布式通信协议

## 1、网络协议：TCP/IP 和UDP/IP

 TCP五层:物理层（物理介质）、数据链路层（硬件接口）、网络层（IP ICMP/IGMP）、传输层（TCP/UDP）、应用层（应用程序）

OSI模型七层： 5 + 表示层 会话层

### 3次握手协议（确认）

你爱我吗？我爱你，我知道了。

伪造IP发送http请求，不给回复，半连接，导致连接队列栈满，
DoS攻击是指故意的攻击网络协议实现的缺陷或直接通过野蛮手段残忍地耗尽被攻击对象的资源。

### 4次挥手协议

客户端发起请求？服务端确认。服务端发起完成请求。客户端确认。


单工 一个方向（发微信），半双工 对讲（不能打断） ，全双工（TCP） 双向 （吵架）


 UDP 不可靠，不建立连接，视频，性能好。

## 2、NIO（同步非阻塞，点餐后干其他事情，干完之后问别人餐好了没有）主动问，AIO（异步非阻塞）（餐好了就通知） 被动通知

BIO 大于发送缓冲区就会阻塞

![](img/50.png) 

NIO底层存在一个I/O调度线程不断扫描Socket缓冲区，发现缓冲区为空，主动通知。

![](img/51.png) 

### TCP Socket 滑动窗口协议（缓冲区）

报文可靠性，流量控制，和限流有点类似，传输的每个部分被分配唯一的连续序列号，接收方使用数字并以正确的顺序放置接收到的数据包（窗口：多个同时收到，全部接收向右滑动），丢弃重复的数据包并识别丢失的数据。

 ![](/img/52.png) 

 ![](/img/53.png) 

## 3、Multicase（组播）dubbo会用到此协议

单播 点对点

广播

组播

基于TCP  

叫一群“美女”，会有一组女生回头。

#  分布式通信-序列化

## 1、简介

java序列化：数据大，效率低，不能跨语言。

通用性、安全性、优化性能。

UID 版本号：保证序列化和反序列化是同一个。

同一个对象存储两次不会叠加，只是引用，如果属性有改变，会对改变的值存储。

XML编码 易懂，跨语言。

HTTP RESTFul 跨语言，无状态，数据大。 JSON(空间大、性能低)

MessagePack 开源序列化框架

Protocol Buffers:压缩字节数小、序列化快、缓存机制。

序列化实现深克隆

浅克隆：复制对象，不复制对象引用。

Dubbo kryo

恰当的序列化协议不仅可以提高系统的通用性、强壮型、安全性、优化性能。同时还能让系统更加易于调试和扩展。



### serialVersionUID的作用

文件流中的class和classpath中的class，也就是修改过后的class，不兼容了，处于安全机制考虑，程序抛出了错误，并且拒绝载入。
从错误结果来看，如果没有为指定的class配置serialVersionUID，那么java编译器会自动给这个class进行一个摘要算法，类似于指纹算法，只要这个文件有任何改动，得到的UID就会截然不同的，可以保证在这么多类中，这个编号是唯一的。

所以，由于没有显指定 serialVersionUID，编译器又为我们生成了一个UID，当然和前面保存在文件中的那个不会一样了，于是就出现了2个序列化版本号不一致的错误。因此，只要我们自己指定了serialVersionUID，就可以在序列化后，去添加一个字段，或者方法，而不会影响到后期的还原，还原后的对象照样可以使用，而且还多了方法或者属性可以用。

Transient关键字

transient关键字表示指定属性不参与序列化。

序列化不保存静态变量的状态。父类没实现序列化接口，子类实现了，父类属性不生效。

要想父类对象也参与序列化操作，那么必须要让父类也实现Serializable接口

对同一个对象进行多次写入，打印出的第一次存储结果和第二次存储结果，只多了5个字节的引用关系。并不会导致文件累加。


序列化实现深度克隆

浅拷贝仅仅复制所拷贝的对象，而不复制它所引用的对象。


深拷贝把要复制的对象所引用的对象都复制了一遍。



### 主流的序列化技术有哪些

JSON/Hessian(2) /xml/protobuf/kryo/MsgPack/FST/thrift/protostuff/Avro

## 2、主流

JSON/Hessian(2)/xml/protobuf/kryo/MsgPack/FST/thrift/protostuff/Avro

#  HTTP

## 1、基于TCP的可靠协议

无状态（cookie+session 保持状态）、多次请求

## 2、服务端和客户端、资源、媒体类型、URI（资源名）和URL。


schema:http/https/ftp 、host:web服务器的ip地址或者域名、

path:资源访问路径、query-string：查询次数

##  3、方法 GET/PUT/POST/HEAD/DELETE

## 4、报文

请求行、请求体、报文

![请求头](/img/2.png) 


## HTTP Request Header 请求头

|Header|解释|示例|
|:----------------:|:--------------------------------------------------:|:----------------:|
|Accept|指定客户端能够接收的内容类型|Accept: text/plain, text/html|
|Accept-Charset|	浏览器可以接受的字符编码集。|	Accept-Charset: iso-8859-5|
|Accept-Encoding|	指定浏览器可以支持的web服务器返回内容压缩编码类型。|	Accept-Encoding: compress, gzip|
|Accept-Language|	浏览器可接受的语言|	Accept-Language: en,zh|
|Accept-Ranges|	可以请求网页实体的一个或者多个子范围字段|	Accept-Ranges: bytes|
|Authorization|	HTTP授权的授权证书|	Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==|
|Cache-Control|	指定请求和响应遵循的缓存机制|	Cache-Control: no-cache|
|Connection|	表示是否需要持久连接。|（HTTP 1.1默认进行持久连接）	Connection: close|
|Cookie|	HTTP请求发送时，会把保存在该请求域名下的所有cookie值一起发送给web服务器。|	Cookie: $Version=1; Skin=new;|
|Content-Length|	请求的内容长度|	Content-Length: 348|
|Content-Type|	请求的与实体对应的MIME信息|	Content-Type: application/x-www-form-urlencoded|
|Date|	请求发送的日期和时间|	Date: Tue, 15 Nov 2010 08:12:31 GMT|
|Expect|	请求的特定的服务器行为|	Expect: 100-continue|
|From|	发出请求的用户的Email|	From: user@email.com|
|Host|	指定请求的服务器的域名和端口号|	Host: www.zcmhi.com|

request消息结构包含三部分：（起始行、首部字段、主体）

METHOD/path/http/version-number

Header-Name:value

空行

主体 optional request body

response

http/version-number status code message

header-name:value

空行

body

状态码

200 OK 客戸端靖求成功

400 Bad Request 客戸端请求错误

401 Unauthorized 请求未经授权,和www-Authenticate一起使用

403 Forbidden 服多器收到请求,但是拒绝提供服务

404 Not Found 请求资源不存在 eg url错误

500 Internal Server Error服各器发生不可预期的错误

503 Server Unavailable 服各器当前不能处理客戸端的请求,一段吋同后可能恢复正常

缓存：静态资源 是否缓存、缓存时间等

ETag：  客户端请求一个页面（A）。 服务器返回页面A，并在给A加上一个ETag。 

客户端展现该页面，并将页面连同ETag一起缓存。 客户再次请求页面A，并将上次请求时服务器返回的ETag一起传递给服务器。 服务器检查该ETag，并判断出该页面自上次客户端请求之后还未被修改，
直接返回响应304（未修改——Not Modified）和一个空的响应体。

HTTP协议的特点

* 1.无状态

cookie+session

* 2.多次请求
* 3.基于TCP协议

## 5、HTTPS

### 服务端发送公钥，公钥被掉包

服务器发送公钥X，公钥X被恶意拦截，第三者发送Y公钥到客户端，客户端通过错误的公钥Y加密发送到服务器，又被第三者拦截，解密Y，用X加密发送到服务端。

![](img/55.png) 

![](img/54.png) 

本地维护了一个受信任的公钥，服务端自己配置数字证书，数字证书传到浏览器会跟客户端算法进行比较，确认服务器端传给客户端的数字证书。

编号是不是客户端所对应的证书的编号。编号相同则请求受信。



数字证书原因、公私钥、对接加密、非对称加密、随机数


1.SSL/TLS 加密socket

ISOC在SSL的基础上发布了升级版本TLS1.2

公钥与私钥的作用是：用公钥加密的内容只能用私钥解密，用私钥加密的内容只能用公钥解密

当A->B资料时，A会使用B的公钥加密，这样才能确保只有B能解开，否则普罗大众都能解开加密的讯息
，就是去了资料的保密性。验证方面则是使用签验章的机制，A传资料给大家时，会以自己的私钥做签章，如此所有收到讯息的人都可以用A的公钥进行验章，
便可确认讯息是由 A 发出来的了

2.工作原理：通过第三方机构，使用第三方机构私钥对我们需要传输的公钥进行加密。
通过算法验证服务器的数字证书，然后服务端生成回话秘钥，双方加解密。少了秘钥的传输过程。（主要是证书无法解密）

证书：证书内容本身的数字签名、证书持有者公钥、证书签名用到的hash算法。

1)客户端https请求：加密、生成随机数（第一个）

2)服务端收到请求后：拿到随机数返回证书（使用第三方机构的私钥加密）、生成随机数（第二个）、返回给客户端

3)客户端拿到证书后做验证：根据并发机构找到本地跟证书、
根据CA得到跟证书公钥，通过公钥对证书解密得到内容摘要A、用证书提供的
算法对证书内容摘要，得到摘要B、通过A和B对比，也就是验证数字签名。

4)验证通过后生成一个随机数（第三个），
通过证书内的公钥对这个随机数加密，发送给服务端、

5)（随机数1+2+3）通过对称加密得到一个秘钥（会话秘钥）。

6)通过会话秘钥对内容进行对称加密传输（对称加密效率高）。

随机数：保证每次建立连接的会话的key是唯一的。客户端公钥加解密，服务端私钥加解密。所有会话通过会话秘钥传输。

少了秘钥传输过程，会话秘钥无法破解。没证书没办法建立socket连接，所以获取会话秘钥也没用。

![](img/56.png) 

* 客户端发起一个https请求

 * 客户端支持的加密方式

 * 客户端生成的随机数（第一个随机数）

* 服务端收到请求后，拿到随机数，返回

 * 证书（颁发机构（CA）、证书内容本身的数字签名（使用第三方机构的私钥加密）、证书持有者的公钥、证书签名用到的hash算法）

 * 生成一个随机数，返回给客户端（第二个随机数）

* 客户端拿到证书以后做验证

 * 根据颁发机构找到本地的跟证书

 * 根据CA得到根证书的公钥，通过公钥对数字签名解密，得到证书的内容摘要 A

 * 用证书提供的算法对证书内容进行摘要，得到摘要 B

 * 通过A和B的对比，也就是验证数字签名

* 验证通过以后，生成一个随机数（第三个随机数），通过证书内的公钥对这个随机数加密，发送给服务器端

* （随机数1+2+3）通过对称加密得到一个密钥。（会话密钥）

* 通过会话密钥对内容进行对称加密传输

摘要不能解密，所以叫摘要

USB双向校验

## 6、RESTful

资源、URI唯一标识、统一接口处理资源请求（POST/GET/PUT/DELETE/HEAD）、无状态（客户端保存cookie）

GET请求，获取Request-URI所标识的资源

POST在Request-URI所标识的资源 后附加新的数据

HEAD 请求获取由Request-URI所标识的资源的响应消息报文

PUT请求服各器存偖一个资源, 并用Request-URI作内其标识DELETE靖求服务器刪除Request-URI所标识的资源

TRACE靖求服多器回送收到的靖求信息,主要用于测试或诊断

CONNECT保留將来使用

OPTIONS请求查询服务器的性能,或者査洵与资源相关的选项和

/ 表示资源层级关系、过滤资源、使用-或者_让URI的可读性更好

统一接口 GET POST PUT DELETE PATCH HEAD/DELETE


类型 ：accept Content-Type 描述格式

资源表述

MIME 类型（）

accept: text/xml   html文件

Content-Type告诉客户端资源的表述形式

资源链接

状态转移，服务器不保存客户端状态。

## 7.get与post区別

1.get方式提交表表单表，单中的数据会在地址栏中显示，而post方法则不会。

2.get方式提交表单是有限制的512字节,而post理论上是无限的。

3.get方式提交表单会在http数据包中的第一行出现,而post在空一行的 body中出现。

## 8.http请求流程

点击网址后，应用层的DNS协议会将网址解析为IP地址；在应用层，浏览器会给web服务器发送一个HTTP请求；在传输层，（上层的传输数据流分段）HTTP数据包会嵌入在TCP报文段中；在网络层中，TCP报文段再嵌入IP数据包中；在网络接口层，IP数据包嵌入到数据帧（以太网数据包）中在网络上传送；数据包经过多个网关的转发到达百度服务器，请求对应端口的服务；请求处理完成之后，服务器发回一个HTTP响应；浏览器以同样的过程读取到HTTP响应的内容（HTTP响应数据包），然后浏览器对接收到的HTML页面进行解析，把网页显示出来呈现给用户。

##  RMI

## 1、RPC :远程方法调用（规范）基于socket。（rmi、webservice、http）

1.Dubbo、Thrif、RMI、WebService、Hessain

## 2、RPC包含的要素

传输协议 TCP/UDP

Message protocol 消息管理

代理 RPC proxy

![](img/57.png) 

## 3、RMI(有代码示例)

使用JRMP（远程信息交换协议），JRMP是专门为java定制的通信协议，java分布式解决方案。

不能重试、bio效率低、不跨平台、java原生序列化效率低、注册中心会挂没办法负载。

Java序列化的两个目的：网络传输、对象持久化。

## 4、WebService 复杂类型丢失

RPC 包含的要素

webservice

协议层：tcp/ip

应用层： http协议

SOAP： http+xml 


分布式通信框架-webservice分析


什么是webservice

webservice也可以叫xml web service webservice, 轻量级的独立的通讯技术

1.	基于web的服务：服务端提供的服务接口让客户端访问

* 跨平台、跨语言的整合方案

为什么要使用webservice

跨语言调用的解决方案

什么时候要去使用webservice

电商平台，订单的物流状态。 

.net实现的webservice服务接口

webservice中的一些概念

WSDL(web service definition language  webservice 定义语言)

webservice服务需要通过wsdl文件来说明自己有什么服务可以对外调用。并且有哪些方法、方法里面有哪些参数

wsdl基于XML（可扩展标记语言）去定义的

1.	 对应一个.wsdl的文件类型

* 定义了webservice的服务器端和客户端应用进行交互的传递数据和响应数据格式和方式

* 一个webservice对应唯一一个wsdl文档

SOAP（simple object access protocal简单对象访问协议）

http+xml

webservice通过http协议发送和接收请求时， 发送的内容（请求报文）和接收的内容（响应报文）都是采用xml格式进行封装

这些特定的HTTP消息头和XML内容格式就是SOAP协议

1.	一种简单、基于HTTP和XML的协议

* soap消息：请求和响应消息

* http+xml报文

SEI（webservice endpoint interface webservice的终端接口）

webservice服务端用来处理请求的接口，也就是发布出去的接口。

Axis/Axis2

apache开源的webservice工具

CXF

Celtix+Xfire 。 用的很广泛，因为集成到了spring

Xfire

高性能的Webservice

HTTP+JSON (新的webservice)

HTTP+XML

![](img/58.png) 

![](img/59.png) 


RMI、 http协议/https、webservice、 TCP协议、UDP协议、socket编程、bio /nio模型、分布式架构、集群、架构演进过程

## 常见架构

1.  MVC


2.  RPC架构

RPC(Remote Procedure Call):远程过程调用。他一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。

代表技术: Thrift、 Hessian 等等


3.  SOA架构

SOA(Service oriented Architecture):面向服务架构

ESB(Enterparise Servce Bus):企业服务总线，服务中介。主要是提供了一个服务于服务之间的交互。

ESB包含的功能如:负载均衡，流量控制，加密处理，服务的监控，异常处理，监控告急等等。

代表技术: Mule、 WSO2


4.  微服务架构

微服务就是一个轻量级的服务治理方案。代表技术: SpringCloud、 dubbo 等等

#  微服务设计原则

1. AKF拆分原则

Y轴(功能)一 关注应用中功能划分，基于不同的业务拆分区 

X轴(水平扩展)一关注水平扩展，也就是”加机器解决问题”

Z轴(数据分区) 一关注服务和数据的优先级划分，如按地域划分


2. 前后端分离原则


4. RestFul的通信风格

#  会话层-DID原则

Design:按照10倍体量设计

Implement:按照3倍体量实现

Deploy:按照1.5倍部署

#  架构师

架构能力规范、预见性、执行力、技术选型，引领团队的发展具备一定的运维能力(服务上的基本的部署工作可以独立完成)知己知彼、百战不殆。

# 微服务架构的优点

对于单一体系结构，开发人员经常面临有限的可重用性和可伸缩性的挑战。但是，通过微服务设计，可以将这个单元分解为不同的模块，
从而简化开发，部署和维护。那么让我们来看看微服务架构的一些主要优点：

技术灵活性

虽然单片架构总是让开发人员寻找“适合工作的正确工具”，但微服务架构在一个封面下提供了多种技术的共存。
可以用多种编程语言编写不同的解耦服务。这不仅使开发人员能够进行实验，还通过添加其他功能和功能来扩展其产品。

提高效率

微服务架构加速了整个创建过程。与单个单元不同，团队可以同时处理软件系统的多个组件。除了提高生产率之外，
还可以更轻松地定位特定组件并专注于它们。单个组件的故障不会影响整个系统。相反，这也简化了错误定位和维护。

产品不是项目

根据Martin Fowler的说法，微服务架构可以帮助企业创建“ 产品而不是项目”。简单来说，微服务架构的使用允许团队聚集在一起并为业务创建功能而不是简单的代码。
整个团队聚集在一起，为不同的功能做出贡献。如果适用，这些可以进一步用于不同的业务。此外，它还创建了一个自主的跨职能团队。

成功的微服务设计的基础知识

现在我们知道微服务架构的优势，但是，我们如何实现完美？我们是否了解微服务设计原则？设计微服务架构的最佳实践是什么？让我们回答这些问题，看看成功的微服务设计的一些基础知识。

### 1.功能范围

通过不同团队同时实施开发和部署以建立或支持与产品分别独特的功能，定义微服务的范围成为非常重要的任务。虽然许多人担心创建“太多”微小的微服务，但通常更常见的是这些微服务过载。


当我们谈论微服务的范围时，我们指的是独立软件模块的功能。微服务作为几乎无状态系统的能力使其能够独立开发。因此，必须确定微服务将实现的功能。
这有助于理解微服务的责任吗？实现每个微服务应该服务的预期功能。不仅要防止过载，还要服务于不同的场景。例如，在单片设置中多次调用一段代码，
创建微服务将使其更易于访问和使用。最小化代码量只会提高效率并避免膨胀的服务。

问题是关于如何定义微服务的范围。虽然没有明确定义的规则集来实现这一目标，但如果您可以定义范围，则可以使用一些指南或最佳实践。以下是您可以用来定义微服务的一些步骤。

第一步是确定在各种模块下复制的代码片段。你经常看到它们重复吗？每次在不同的模块中设置它们需要花费多少精力？如果所有这些的答案都很高，那么微服务的范围就是只处理重复的代码片段。
您可以采取的另一个步骤是检查模块是否不依赖于其他模块或更简单的术语，检查模块是否可能与其余服务松散耦合。如果是这样，那么微服务的范围将是整个模块的范围。
在定义范围时要考虑的另一个非常重要的指标是检查功能是否将用于重负载。这将检查微服务是否必须在不久的将来扩展。如果确实如此，
那么将可扩展位定义为微服务的范围而不是将其与其他功能相结合是一个好主意。

### 2.高内聚力与松耦合

任何微服务的主要动机是使服务彼此独立。这意味着可以编辑，更新或部署新服务，而不会妨碍任何其他服务。如果相互依赖性很低，
这是可能的。松散耦合的系统是一个服务对其他服务知之甚少或什么都不知道的系统。

在将整体架构分解为更小的服务或组件时，将类似功能组合起来非常重要。将相关逻辑组合成单个单元是已知的内聚。
内聚力越高，微服务架构越好。较低的内聚力表明不同服务之间的通信过多导致系统性能较差。

### 3.独特的身份识别来源

遵循微服务设计的基本原则，任何服务都必须成为系统其余部分的唯一识别源。让我们举一个例子来理解这种情况。

在电子商务网站上下订单后，向用户提供订单ID。生成此订单ID包含有关订单的所有信息。作为微服务，订单ID是有关订单服务的任何信息的唯一来源。
因此，如果任何其他服务寻求关于订单服务的信息，则订单ID充当信息源而不是其实际属性。

### 4. API集成

将整体设计分解为多种服务意味着这些服务将协调并协同工作以形成系统。但是，这些服务如何沟通？想象一下，使用多种技术来创建不同的服务。它们如何相互关联？


嗯，简单的答案是使用API​​（应用程序编程接口）。微服务设计的基础是使用正确的API。这对于维护服务和客户端调用之间的通信至关重要。轻松过渡和执行对于正常运行非常重要。

<br>创建API时需要注意的另一个重要事项是业务领域。域的这种定义将简化区分功能的过程。有几个客户端在系统外部。这些客户端可以是其他应用程序或用户。
无论何时调用业务逻辑，它都由适配器（消息网关或Web控制器）处理，该适配器返回请求并对数据库进行更改。

### 5.数据存储隔离

为特定服务存储的任何数据都应该对该特定服务保密。这意味着对数据的任何访问都应归服务所有。此数据只能通过API与任何其他服务共享。
这对于保持对数据的有限访问并避免“服务耦合”非常重要。基于用户的数据分类很重要，可以通过命令和查询责任分离（CQRS）来实现。

### 6.交通管理

一旦设置了API并且系统启动并运行，不同服务的流量就会有所不同。流量是客户端发送给特定服务的呼叫。在现实世界中，服务可能运行缓慢，
从而导致调用花费更多时间。或者服务可能充斥着呼叫。在这两种情况下，性能都会受到影响，甚至导致软件或硬件崩溃。

这种高流量需求需要管理。呼叫和被叫的特定方式是流畅的流量的答案。服务应该能够终止任何导致延迟并影响性能的实例。

这也可以使用称为“自动缩放”的过程来实现，该过程包括在需要时通过快速动作持续跟踪服务。在某些情况下，
“断路器模式”对于提供在呼叫中断或服务无响应时可用的任何不完整信息非常重要。

### 7.自动化流程

独立设计的微服务应该能够自行运行。自动化将实现自我部署和功能，而无需任何干预。此过程使服务本质上具有云原生性，
并且能够在任何环境中部署。但要实现这一目标，让DevOps团队不断致力于服务的发展是非常重要的。

### 8.最小数据库表（最好是隔离表）

访问数据库表以获取数据可能是一个漫长的过程。它可能需要时间和精力。在设计微服务时，主要动机应该围绕业务功能而不是数据库及其工作。
为了确保这一点，即使数据条目达到数百万，微服务设计也应该只有几个表。除了最小数量，关注业务是关键。

### 9.持续监测

想象一下，将单片架构分解为微服务设计。这需要大量的时间和资源。在传统工具的帮助下监控所做的所有更改并不容易。插入数据层和缓存会提高性能，但却难以监控整个过程。

因此，为了设计微服务架构，重要的是建立用于主动监视中心位置的数据存储的过程。这有助于反映频繁的变化，而不会影响系统的性能。在一个常见的场景中，微服务监控工具将监控单个服务，
然后通过将数据存储在一个集中位置来组合数据。这是遵循微服务设计原则的必要步骤。

实现API在成功的微服务架构中扮演的关键角色。还必须有一个持续监控API性能的过程。API性能监控对于任何微服务架构都至关重要，以确保功能在速度，响应性和产品的整体性能方面始终如一。

微服务架构的局限性

## 虽然微服务是降低整体结构的最佳方式，但它有其自身的一些缺点

### 1.开发环境超载

随着应用程序及其数据库的增长，代码库也在不断扩展。随着针对每个微服务的代码扩展，它会使每个加载的应用程序的开发环境过载。这可能导致生产力的重大延迟。

### 2. DevOps复杂性

单功能微服务的开发和部署并非易事。使用多种技术并创建API来集中系统是一项挑战。这需要一个经验丰富的DevOps团队。采购这样一个经验丰富的DevOps团队对于维护基于微服务的应用程序的复杂性至关重要。

### 3.增加资源和网络使用

由于多个组件协同工作，因此在某种程度上彼此进行通信非常重要。此通信将导致网络使用量增加。这需要高速可靠的网络连接。此外，运行这些应用程序的费用也会增加。所有服务都单独运行，增加了运营成本。

### 4.测试

测试应用程序可能具有挑战性，因为有单独的组件。与单片应用程序相比，微服务需要更长的时间进行测试，并且在出现任何错误时更加复杂。有时，由于测试最终会影响整个应用程序，可能会导致延迟。

### 5.安全

在Web应用程序方面，安全性至关重要。使用微服务，实现这一点很困难。当存在独立模块的集群时，每个模块都需要遵守为整个系统定义的认证和授权规范。

除此之外，每个模块可能与其他模块通信，跟踪数据流变得非常困难。需要其他措施，例如具有负载平衡的API网关，以确保行为一致。这些额外的步骤导致每个微服务的开销。

### 6.应用程序的复杂性

由于微服务是独立组件，因此每个微服务通常都有一个最适合其需求的技术堆栈。例如，机器学习模块可能使用python堆栈，而计量服务可能使用Java堆栈，UI服务可能使用MEAN堆栈。
这会导致复杂性，因为资源池和管理和构建新功能所需的技能将非常高。

### 7.高初始投资

微服务独立运行，它们需要独立的容器或资源来运行它们。每个项目可能有很多微服务一起工作，需要更高的投资来设置包括微服务，安全容器，负载平衡器，API网关等的所有集群。











