## 一、分布式架构概述
### 1、分布式
一个业务拆分成多个子系统，部署在不同的服务器上（相互调用）。
### 2、集群
<br>同一个业务，部署在多个服务器上。高可用。
### 3、大型网站
 访问量（tps每秒传输的事物处理个数、qps每秒查询率）
<br> 数据量（存储数据量）
### 4、读写分离
### 5、搜索引擎
### 6、缓存、限流、降级、分库、表拆分（500w~1000w）
### 7、解决session跨域
 cookie 存sessionid，服务端Tomcat用ConcuurentMap存储
<br> session sticky
<br> session replication 
<br> session 集中存储 db、缓存服务器
<br> cookie （主流） access token（userid/token/timestamp）
<br> soa 架构和微服务架构
## 二、分布式通信协议
### 1、网络协议：TCP/IP 和UDP/IP
 TCP五层:物理层（物理介质）、数据链路层（硬件接口）、网络层（IP ICMP/IGMP）、传输层（TCP/UDP）、应用层（应用程序）
<br><br> 3次握手协议（确认）。
<br>你爱我吗？我爱你，我知道了。
<br>伪造IP发送http请求，不给回复，导致连接队列栈满，
DoS攻击是指故意的攻击网络协议实现的缺陷或直接通过野蛮手段残忍地耗尽被攻击对象的资源。
<br><br> 4次挥手协议。
<br>你爱我吗？我收到了。我爱你。我收到了。
<br><br> 单工，半双工 对讲 ，全双工（TCP）吵架
<br><br> UDP 不可靠，不建立连接，视频，性能好。
### 2、NIO（同步非阻塞）主动问，AIO（异步非阻塞） 被动通知。
 滑动窗口协议（缓冲区）：传输的每个部分被分配唯一的连续序列号，接收方使用数字并以正确的顺序放置接收到的数据包，丢弃重复的数据包并识别丢失的数据。
### 3、Multicase（组播）
基于TCP
<br>叫一群“美女”，会有一组女生回头。
## 三、分布式通信-序列化
### 1、简介
java序列化：数据大，效率低，不能跨语言。
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
JSON/Hessian(2)/xml/protobuf/kryo/MsgPack/FST/thrift/protostuff/Avro
## 四、HTTP
### 1、基于TCP的可靠协议
、无状态（cookie+session 保持状态）、多次请求
### 2、服务端和客户端、资源、媒体类型、URI（资源名）和URL。
<br>schema:http/https/ftp 、host:web服务器的ip地址或者域名、
path:资源访问路径、query-string：查询次数
###  3、方法 GET/PUT/POST/HEAD/DELETE
### 4、报文
<br><br>
![请求头](https://github.com/gaoyuanyuan2/distributed/blob/master/img/2.png) 
<br><br>
#### HTTP Request Header 请求头
|Header|解释|示例|
|:--:|:--:|:--:|
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
<br><br>
<br> request消息结构包含三部分：（起始行、首部字段、主体）
<br>METHOD/path/http/version-number
<br>Header-Name:value
<br>空行
<br>主体 optional request body
<br><br> response
<br>http/version-number status code message
<br>header-name:value
<br>空行
<br>body
<br>状态码
<br><br>200 OK 客戸端靖求成功
<br>400 Bad Request 客戸端靖求有語法錯謨,不能岐服各器所理解
<br>401 Unauthorized 请求未经授权,和www-Authenticate一起使用
<br>403 Forbidden 服多器收到請求,但是拒绝提供服务
<br>404 Not Found 请求资源不存在 eg url错误
<br>500 Internal Server Error服各器发生不可预期的错误
<br>503 Server Unavailable 服各器当前不能处理客戸端的靖求,一段吋同后可能恢夏正常
<br><br>缓存：静态资源
### 5、HTTPS
<br>1.SSL/TLS 加密socket
<br> ISOC在SSL的基础上发布了升级版本TLS1.2
<br>公钥与私钥的作用是：用公钥加密的内容只能用私钥解密，用私钥加密的内容只能用公钥解密
<br>当A->B资料时，A会使用B的公钥加密，这样才能确保只有B能解开，否则普罗大众都能解开加密的讯息
，就是去了资料的保密性。验证方面则是使用签验章的机制，A传资料给大家时，会以自己的私钥做签章，如此所有收到讯息的人都可以用A的公钥进行验章，
便可确认讯息是由 A 发出来的了
<br><br>2.工作原理：通过第三方机构，使用第三方机构私钥对我们需要传输的公钥进行加密。
通过算法验证服务器的数字证书，然后服务端生成回话秘钥，双方加解密。少了秘钥的传输过程。（主要是证书无法解密）
<br>证书：证书内容本身的数字签名、证书持有者公钥、证书签名用到的hash算法。
<br>1)客户端https请求：加密、生成随机数（第一个）
<br>2)服务端收到请求后：拿到随机数返回证书（使用第三方机构的私钥加密）、生成随机数（第二个）、返回给客户端
<br>3)客户端拿到证书后做验证：根据并发机构找到本地跟证书、
根据CA得到跟证书公钥，通过公钥对证书解密得到内容摘要A、用证书提供的
算法对证书内容摘要，得到摘要B、通过A和B对比，也就是验证数字签名。
<br>4)验证通过后生成一个随机数（第三个），
通过证书内的公钥对这个随机数加密，发送给服务端、
<br>5)（随机数1+2+3）通过对称加密得到一个秘钥（会话秘钥）。
<br>6)通过会话秘钥对内容进行对称加密传输（对称加密效率高）。
### 6、RESTful
<br>资源、URI唯一标识、统一接口处理资源请求（POST/GET/PUT/DELETE/HEAD）、无状态（客户端保存cookie）
<br>GET靖求 荻取Request-URI所标识的资源
<br>POST在Request-URI所标识的资源 后附加新的数据
<br>HEAD 靖求荻取由Request-URI所标识的资源的响应消息报文
<br>PUT靖求服各器存偖一个资源, 并用Request-URI作内其标识DELETE靖求服务器刪除Request-URI所标识的资源
<br>TRACE靖求服多器回送收到的靖求信息,主要用于测试或诊断
<br>CONNECT保留將来使用
<br>OPTIONS请求查询服务器的性能,或者査洵与资源相关的选项和
<br> / 表示资源层级关系、?过滤资源、使用-或者_让URI的可读性更好
<br>统一接口 GET POST PUT DELETE PATCH HEAD/DELETE
<br>类型 ：accept Content-Type 描述格式
<br>资源链接
<br>状态转移，服务器不保存客户端状态。
### 7.get与post区別
<br>1-get方式提交表単表単中的数据会在地址桜中昆示.而post方法則不会。
<br>2.get方式提交表単K度是有限制的512字帯,而post理詑上是无限的。
<br>3.get方式提交表単会在htp数据包中的第- -行出現,而post在空- -行的 body中出現4get方式永透是己字符方式提交.而post既可以已字篠也可以已字帯.

## 五、RMI
### 1、RPC :远程方法调用（规范）基于socket。（rmi、webservice、http）
1.Dubbo、Thrif、RMI、WebService、Hessain
### 2、RPC包含的要素
传输协议 TCP/UDP
<br>Message protocol 消息管理
<br>代理 RPC proxy
### 3、RMI
使用JRMP（远程信息交换协议），JRMP是专门为java定制的通信协议，java分布式解决方案。
<br>不能重试、bio效率低、不跨平台、java原生序列化效率低、注册中心会挂没办法负载。
<br>Java序列化的两个目的：网络传输、对象持久化。
### 4、webservice
1.跨语言调用
<br>2.WSDL 定义语言，.wsdl的文件类型、一个webservice对应唯一一个wsdl文档、调用关系链
<br>3.SOAP 简单对象访问协议，http+xml 
<br>4.SEI webservice 的终端接口，发布出去的接口
<br>5.复杂类型会丢失、大量数据效率低
<br>6.Axis、Axis2
<br>CXF Celtic+Xfire ，spring整合
<br>Xfire 高性能
<br>SpringMVC新的webservice
## 六、Zookeeper 分布式协调服务
















