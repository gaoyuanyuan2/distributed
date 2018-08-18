## 分布式协调服务-zookeeper
### 1、分布式环境的特点
<br>1.分布性
<br><br>2.并发性 程序运行过程中，并发性操作是很常见的。比如同一个分布式系统中的多个节点，同时访问一个共享资源。数据库、分布式存储
<br><br>3.无序性 进程之间的消息通信，会出现顺序不一致问题
### 2、分布式环境下面临的问题
<br>1.网络通信 网络本身的不可靠性，因此会涉及到一些网络通信问题
<br><br>2.网络分区(脑裂)当网络发生异常导致分布式系统中部分节点之间的网络延时不断增大，最终导致组成分布式架构的所有节点，只有部分节点能够正常通信
<br><br>3.三态 在分布式架构里面，除了成功、失败、超时
<br><br>4.分布式事务 ACID(原子性、一致性、隔离性、持久性)
<br><br>5.中心化和去中心化
<br><br>6.冷备或者热备
### 3、选举
<br>分布式架构里面，很多的架构思想采用的是：当集群发生故障的时候，集群中的人群会自动“选举”出一个新的领导。
<br>最典型的是： zookeeper / etcd
### 4、经典的CAP/BASE理论
<br>1.CAP
<br>C（一致性 Consistency）: 所有节点上的数据，时刻保持一致
<br>可用性（Availability）：每个请求都能够收到一个响应，无论响应成功或者失败
<br>分区容错 （Partition-tolerance）：表示系统出现脑裂以后，可能导致某些server与集群中的其他机器失去联系
<br>CP  /AP
<br>CAP理论仅适用于原子读写的Nosql场景，不适用于数据库系统
<br><br>2.BASE
<br>基于CAP理论，CAP理论并不适用于数据库事务（因为更新一些错误的数据而导致数据出现紊乱，无论什么样的数据库高可用方案都是
徒劳），虽然XA事务可以保证数据库在分布式系统下的ACID特性，但是会带来性能方面的影响；
<br>eBay尝试了一种完全不同的套路，放宽了对事务ACID的要求。提出了BASE理论
Basically available ： 数据库采用分片模式， 把100W的用户数据分布在5个实例上。如果破坏了其中一个实例，仍然可以保证
80%的用户可用
soft-state：在基于client-server模式的系统中，server端是否有状态，决定了系统是否具备良好的水平扩展、负载均衡、故障恢复等特性。
Server端承诺会维护client端状态数据，这个状态仅仅维持一小段时间, 这段时间以后，server端就会丢弃这个状态，恢复正常状态
<br>Eventually consistent：数据的最终一致性
### 5、初步认识zookeeper
<br>zookeeper是一个开源的分布式协调服务，是由雅虎创建的，基于googlechubby。
<br><br>1.zookeeper是什么
<br>分布式数据一致性的解决方案
<br><br>2.zookeeper能做什么
<br>数据的发布/订阅（配置中心:disconf）、 负载均衡（dubbo利用了zookeeper机制实现负载均衡） 、命名服务、
<br>master选举(kafka、hadoop、hbase)、分布式队列、分布式锁
<br><br>3.zookeeper的特性
<br>1)顺序一致性
<br>从同一个客户端发起的事务请求，最终会严格按照顺序被应用到zookeeper中
<br><br>2)原子性
<br>所有的事务请求的处理结果在整个集群中的所有机器上的应用情况是一致的，也就是说，要么整个集群中的所有机器都成功应用了某一事务、
要么全都不应用
<br><br>3)可靠性
<br>一旦服务器成功应用了某一个事务数据，并且对客户端做了响应，那么这个数据在整个集群中一定是同步并且保留下来的
<br><br>4)实时性
<br>一旦一个事务被成功应用，客户端就能够立即从服务器端读取到事务变更后的最新数据状态；（zookeeper仅仅保证在一定时间内，近实时）
### 6、zookeeper安装
<br>单机环境安装
<br><br>1.	下载zookeeper的安装包
<br>http://apache.fayea.com/zookeeper/stable/zookeeper-3.4.10.tar.gz
<br><br>2.	解压zookeeper
<br>tar -zxvf zookeeper-3.4.10.tar.gz
<br><br>3.	cd到 ZK_HOME/conf  , copy一份zoo.cfg
<br>cp  zoo_sample.cfg  zoo.cfg
<br><br>4.	sh zkServer.sh
{<br>start|start-foreground|stop|restart|status|upgrade|print-cmd}
<br><br>5.	sh zkCli.sh -server  ip:port
### 7、集群环境
<br>zookeeper集群, 包含三种角色: leader / follower /observer
<br><br>1.observer
<br><br>1)observer 是一种特殊的zookeeper节点。可以帮助解决zookeeper的扩展性（如果大量客户端访问我们zookeeper集群，需要增加zookeeper集群机器数量。从而增加zookeeper集群的性能。 
导致zookeeper写性能下降， zookeeper的数据变更需要半数以上服务器投票通过。造成网络消耗增加投票成本）
<br><br>2)observer不参与投票。 只接收投票结果。
<br><br>3)不属于zookeeper的关键部位。
<br><br>
![整体图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/1.png) 
<br><br>
<br><br>2.	在zoo.cfg里面增加
<br>peerType=observer
<br>server.1=192.168.11.129:2183:3181:observer
<br>server.2=192.168.11.131:2183:3181
<br>server.3=192.168.11.135:2183:3181
<br><br>第一步： 修改配置文件
<br>server.id=host:port:port
<br>id的取值范围： 1~255； 用id来标识该机器在集群中的机器序号
<br>2183是zookeeper的端口； 
<br>3181表示leader选举的端口
<br><br>第二步：创建myid
<br>在每一个服务器的dataDir目录下创建一个myid的文件，文件就一行数据，数据内容是每台机器对应的server ID的数字
<br><br>第三步：启动zookeeper
### 8、zoo.cfg配置文件分析
<br>tickTime=2000  zookeeper中最小的时间单位长度 （ms）
<br>initLimit=10follower节点启动后与leader节点完成数据同步的时间
<br>syncLimit=5leader节点和follower节点进行心跳检测的最大延时时间
<br>ataDir=/tmp/zookeeper表示zookeeper服务器存储快照文件的目录
<br>dataLogDir表示配置 zookeeper事务日志的存储路径，默认指定在dataDir目录下
<br>clientPort表示客户端和服务端建立连接的端口号： 2181
### 9、zookeeper中的一些概念
<br>1.数据模型
<br>zookeeper的数据模型和文件系统类似，每一个节点称为：znode.  是zookeeper中的最小数据单元。每一个znode上都可以
<br>保存数据和挂载子节点。 从而构成一个层次化的属性结构
<br><br>2.节点特性
<br>持久化节点  ： 节点创建后会一直存在zookeeper服务器上，直到主动删除
<br>持久化有序节点 ：每个节点都会为它的一级子节点维护一个顺序
<br>临时节点 ： 临时节点的生命周期和客户端的会话保持一致。当客户端会话失效，该节点自动清理
<br>临时有序节点 ： 在临时节点上多勒一个顺序性特性
<br><br>3.会话
<br><br>
![整体图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/3.png) 
<br><br>
<br><br>4.Watcher
<br>zookeeper提供了分布式数据发布/订阅,zookeeper允许客户端向服务器注册一个watcher监听。当服务器端的节点触发指定事件的时候
<br>会触发watcher。服务端会向客户端发送一个事件通知
<br>watcher的通知是一次性，一旦触发一次通知后，该watcher就失效
<br><br>5.ACL
<br>zookeeper提供控制节点访问权限的功能，用于有效的保证zookeeper中数据的安全性。避免误操作而导致系统出现重大事故。
<br>CREATE /READ/WRITE/DELETE/ADMIN
### 10、zookeeper的命令操作
<br>1. create [-s] [-e] path data acl
<br>-s表示节点是否有序
<br>-e表示是否为临时节点
<br>默认情况下，是持久化节点
<br><br>2. get path [watch]
<br>获得指定 path的信息
<br><br>3.set path data [version]
<br>修改节点 path对应的data
<br>乐观锁的概念
<br>数据库里面有一个 version字段去控制数据行的版本号
<br><br>4.delete path [version]
<br>删除节点
<br>stat信息
<br>cversion = 0子节点的版本号
<br>aclVersion = 0表示acl的版本号，修改节点权限
<br>dataVersion = 1表示的是当前节点数据的版本号
<br><br>5.事务
<br>czxid节点被创建时的事务ID
<br>mzxid节点最后一次被更新的事务ID
<br>pzxid当前节点下的子节点最后一次被修改时的事务ID
<br><br>6.time
<br>ctime = Sat Aug 05 20:48:26 CST 2017
<br>mtime = Sat Aug 05 20:48:50 CST 2017
<br><br>
cZxid = 0x500000015
<br>ctime = Sat Aug 05 20:48:26 CST 2017
<br>mZxid = 0x500000016
<br>mtime = Sat Aug 05 20:48:50 CST 2017
<br>pZxid = 0x500000015
<br>cversion = 0
<br>dataVersion = 1
<br>aclVersion = 0
<br>ephemeralOwner = 0x0创建临时节点的时候（临时节点不能创建子节点），会有一个sessionId 。 该值存储的就是这个sessionid
<br>dataLength = 3数据值长度
<br>numChildren = 0子节点数
### 11、javaAPI的使用
<br>1.	导入jar包
```Xml
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.8</version>
</dependency>
```
<br><br>2.	具体见代码
<br>zkclient
<br>curator




































































































