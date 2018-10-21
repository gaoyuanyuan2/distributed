## 一、分布式架构概述
![](/img/25.jpg) 
### 大数据：数据分析 用户行为 兴趣爱好 活动运营用户画像
### 1、分布式 （任务分解、节点通信）
<br>一个业务拆分成多个子系统，部署在不同的服务器上（相互调用）。
<br><br>去IOE
<br>IBM小型机 、 Oracle Rac、 EMC存储设备
<br>PC            mysql    maria db   
<br><br>2013年5月17号，最后一台IMB小型机下线
<br>单机计算机的架构->分布式计算机架构
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
<br> session sticky （请求落在同一个服务器上）
<br> session replication  （复制）
<br> session 集中存储 db、缓存服务器
<br> cookie （主流）保存在客户端  access token（userid/token/timestamp）加密，拦截器解析token， 判断有效期，用户是否存在
<br>好处：无状态，无需存储在服务端。
<br> soa 架构和微服务架构
## 二、分布式通信协议
### 1、网络协议：TCP/IP 和UDP/IP
 TCP五层:物理层（物理介质）、数据链路层（硬件接口）、网络层（IP ICMP/IGMP）、传输层（TCP/UDP）、应用层（应用程序）
<br>OSI模型七层 5 + 表达层 应用层
<br><br> 3次握手协议（确认）。
<br>你爱我吗？我爱你，我知道了。
<br>伪造IP发送http请求，不给回复，半连接，导致连接队列栈满，
DoS攻击是指故意的攻击网络协议实现的缺陷或直接通过野蛮手段残忍地耗尽被攻击对象的资源。
<br><br> 4次挥手协议。
<br>客户端发起请求？服务端确认。服务端发起完成请求。客户端确认。
<br><br> 单工 一个方向（发微信），半双工 对讲（不能打断） ，全双工（TCP） 双向 （吵架）
<br><br> UDP 不可靠，不建立连接，视频，性能好。
### 2、NIO（同步非阻塞，点餐后干其他事情，干完之后问别人餐好了没有）主动问，AIO（异步非阻塞）（餐好了就通知） 被动通知
BIO 大于发送缓冲区就会阻塞

![](img/50.png) 
<br>NIO底层存在一个I/O调度线程 不断扫描Socket缓冲区，发现缓冲区为空，主动通知
![](img/51.png) 
<br> TCP Socket 滑动窗口协议（缓冲区）：报文可靠性，流量控制，和限流有点类似，传输的每个部分被分配唯一的连续序列号，
 接收方使用数字并以正确的顺序放置接收到的数据包（窗口：多个同时收到，全部接收向右滑动），
 丢弃重复的数据包并识别丢失的数据。
 ![](/img/52.png) 
 ![](/img/53.png) 
### 3、Multicase（组播）dubbo会用到此协议
<br>单播 点对点
<br>广播
<br>组播
<br><br>基于TCP  
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
无状态（cookie+session 保持状态）、多次请求
### 2、服务端和客户端、资源、媒体类型、URI（资源名）和URL。
<br>schema:http/https/ftp 、host:web服务器的ip地址或者域名、
path:资源访问路径、query-string：查询次数
###  3、方法 GET/PUT/POST/HEAD/DELETE
### 4、报文
<br><br>
![请求头](/img/2.png) 
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

<br> request消息结构包含三部分：（起始行、首部字段、主体）
<br>METHOD/path/http/version-number
<br>Header-Name:value
<br>空行
<br>主体 optional request body
<br><br> response
<br><br>ETag：  客户端请求一个页面（A）。 服务器返回页面A，并在给A加上一个ETag。 
客户端展现该页面，并将页面连同ETag一起缓存。 客户再次请求页面A，并将上次请求时服务器返回的ETag一起传递给服务器。 服务器检查该ETag，并判断出该页面自上次客户端请求之后还未被修改，
直接返回响应304（未修改——Not Modified）和一个空的响应体。
<br><br>http/version-number status code message
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
<br>1.get方式提交表表单表，单中的数据会在地址栏中显示.而post方法则不会。
<br>2.get方式提交表单是有限制的512字节,而post理论上是无限的。
<br>3.get方式提交表单会在http数据包中的第一行出现,而post在空一行的 body中出现。
### 8.http请求流程
<br>点击网址后，应用层的DNS协议会将网址解析为IP地址；在应用层，浏览器会给web服务器发送一个HTTP请求；在传输层，（上层的传输数据流分段）HTTP数据包会嵌入在TCP报文段中；在网络层中，TCP报文段再嵌入IP数据包中；在网络接口层，IP数据包嵌入到数据帧（以太网数据包）中在网络上传送；数据包经过多个网关的转发到达百度服务器，请求对应端口的服务；请求处理完成之后，服务器发回一个HTTP响应；浏览器以同样的过程读取到HTTP响应的内容（HTTP响应数据包），然后浏览器对接收到的HTML页面进行解析，把网页显示出来呈现给用户。
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
## 六、会话层-DID原则
<br>Design:按照10倍体量设计
<br><br>Implement:按照3倍体量实现
<br><br>Deploy:按照1.5倍部署
## 七、架构师
<br>架构能力规范、预见性、执行力、技术选型，引领团队的发展具备一定的运维能力(服务上的基本的部署工作可以独立完成)知己知彼、百战不殆。

















