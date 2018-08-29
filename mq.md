## MQ
#### 异步消息、解耦、流量削峰
### 1、ActiveMQ
#### 1. 什么是MOM
<br>面向消息的中间件，使用消息传送提供者来协调消息传输操作。 MOM需要提供API和管理工具。 客户端调用api。 把消息发送到消息传送提供者指定的目的地
在消息发送之后，客户端会技术执行其他的工作。并且在接收方收到这个消息确认之前。提供者一直保留该消息
#### 2. 消息传递
<br><br>1) 点对点(p2p)
<br><br>a.	如果session关闭时，有一些消息已经收到，但还没有被签收，那么当消费者下次连接到相同的队列时，消息还会被签收
<br><br>b.	如果用户在receive方法中设定了消息选择条件，那么不符合条件的消息会留在队列中不会被接收
<br><br>c.	队列可以长久保存消息直到消息被消费者签收。消费者不需要担心因为消息丢失而时刻与jmsprovider保持连接状态
<br><br>2) 发布订阅(pub/sub)
<br><br>a.	订阅可以分为非持久订阅和持久订阅
<br><br>b.	当所有的消息必须接收的时候，则需要用到持久订阅。反之，则用非持久订阅
<br><br>3. 消息组成
<br><br>a. 消息头
<br>包含消息的识别信息和路由信息
<br><br>b. 消息体
<br>TextMessage
<br>MapMessage
<br>BytesMessage
<br>StreamMessage   输入输出流
<br>ObjectMessage  可序列化对象
#### 3. JMS的可靠性机制
<br><br>JMS消息之后被确认后，才会认为是被成功消费。消息的消费包含三个阶段： 客户端接收消息、客户端处理消息、消息被确认
<br><br>1) 事务性会话
<br>消息会在session.commit以后自动签收
<br><br>2) 非事务性会话
<br>在该模式下，消息何时被确认取决于创建会话时的应答模式
<br><br>AUTO_ACKNOWLEDGE
<br>当客户端成功从recive方法返回以后，或者[MessageListener.onMessage]方法成功返回以后，会话会自动确认该消息
<br><br>CLIENT_ACKNOWLEDGE
<br>客户端通过调用消息的textMessage.acknowledge();确认消息。
<br>在这种模式中，如果一个消息消费者消费一共是10个消息，那么消费了5个消息，然后在第5个消息通过textMessage.acknowledge()，那么之前的所有消息都会被消确认
<br><br>DUPS_OK_ACKNOWLEDGE
<br>延迟确认
<br><br>3) 本地事务
<br>在一个JMS客户端，可以使用本地事务来组合消息的发送和接收。JMSSession接口提供了commit和rollback方法。
JMSProvider会缓存每个生产者当前生产的所有消息，直到commit或者rollback，commit操作将会导致事务中所有的消息被持久存储；rollback意味着JMSProvider将会清除此事务下所有的消息记录。在事务未提交之前，消息是不会被持久化存储的，也不会被消费者消费
事务提交意味着生产的所有消息都被发送。消费的所有消息都被确认； 
事务回滚意味着生产的所有消息被销毁，消费的所有消息被恢复，也就是下次仍然能够接收到发送端的消息，除非消息已经过期了
#### 4. ActiveMQ支持的传输协议
<br>client端和broker端的通讯协议
<br>TCP、UDP 、NIO、SSL、Http（s）、vm
#### 5. ActiveMQ持久化存储
 <br><br>![持久化存储](https://github.com/gaoyuanyuan2/distributed/blob/master/img/6.png) 
#### 6. 丢失的消息
<br>一些consumer连接到broker1、消费broker2上的消息。消息先被broker1从broker2消费掉，然后转发给这些consumers。假设，
转发消息的时候broker1重启了，这些consumers发现brokers1连接失败，通过failover连接到broker2.
但是因为有一部分没有消费的消息被broker2已经分发到broker1上去了，这些消息就好像消失了。除非有消费者重新连接到broker1上来消费。
<br><br>消息回流处理
#### 7. 高可用方案
<br><br>1) 通过zookeeper+activemq实现高可用方案
<br>（master/slave模型）
<br><br>2) 通过zookeeper+activemq实现高可用方案
<br>（master/slave模型）
<br><br>3) 基于共享文件系统的主从方案
<br>挂载网络磁盘，将数据文件保存到指定磁盘上即可完成master/slave模式
#### 8.源码 
<br>1) 责任链模式
### 2、RabbitMQ
<br>1. Exchange Type有三种：fanout、direct、topic。
<br><br>1) fanout:把所有发送到该Exchange的消息投递到所有与它绑定的队列中。
<br><br>2) direct:把消息投递到那些binding key与routing key完全匹配的队列中。
<br><br>3) topic:将消息路由到binding key与routing key模式匹配的队列中。
### 3、Kafka
#### 1. zookeeper上注册的节点信息
<br>cluster, controller, controller_epoch, brokers, zookeeper, admin, isr_change_notification, consumers, latest_producer_id_block, config
#### 2. 消息
<br>消息是kafka中最基本的数据单元。消息由一串字节构成，其中主要由key和value构成，key和value也都是byte数组。key的主要作用是根据一定的策略，将消息路由到指定的分区中，这样就可以保证包含同一key的消息全部写入到同一个分区中，key可以是null。为了提高网络的存储和利用率，生产者会批量发送消息到kafka，并在发送之前对消息进行压缩
#### 3. topic&partition
<br>Topic是用于存储消息的逻辑概念，可以看作一个消息集合。每个topic可以有多个生产者向其推送消息，也可以有任意多个消费者消费其中的消息
<br>每个topic可以划分多个分区（每个Topic至少有一个分区），同一topic下的不同分区包含的消息是不同的。每个消息在被添加到分区时，都会被分配一个offset（称之为偏移量），它是消息在此分区中的唯一编号，kafka通过offset保证消息在分区内的顺序，offset的顺序不跨分区，即kafka只保证在同一个分区内的消息是有序的；
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/10.png) 
<br><br>Partition是以文件的形式存储在文件系统中，存储在kafka-log目录下，命名规则是：<topic_name>-<partition_id>
#### 4. kafka的高吞吐量的因素
<br>1)	顺序写的方式存储数据 ；
<br><br>2)	批量发送；在异步发送模式中。kafka允许进行批量发送，也就是先讲消息缓存到内存中，然后一次请求批量发送出去。这样减少了磁盘频繁io以及网络IO造成的性能瓶颈
batch.size每批次发送的数据大小
<br>linger.ms间隔时间
<br><br>3)	零拷贝
<br><br>消息从发送到落地保存，broker维护的消息日志本身就是文件目录，每个文件都是二进制保存，生产者和消费者使用相同的格式来处理。在消费者获取消息时，服务器先从硬盘读取数据到内存，然后把内存中的数据原封不懂的通过socket发送给消费者。虽然这个操作描述起来很简单，但实际上经历了很多步骤
<br><br> a. 操作系统将数据从磁盘读入到内核空间的页缓存
<br><br> b. 应用程序将数据从内核空间读入到用户空间缓存中
<br><br> c. 应用程序将数据写回到内核空间到socket缓存中
<br><br> d. 操作系统将数据从socket缓冲区复制到网卡缓冲区，以便将数据经网络发出
<br><br>通过“零拷贝”技术可以去掉这些没必要的数据复制操作，同时也会减少上下文切换次数
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/11.png)
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/9.png)
#### 5. 日志保留策略
<br>无论消费者是否已经消费了消息，kafka都会一直保存这些消息，但并不会像数据库那样长期保存。为了避免磁盘被占满，kafka会配置响应的保留策略（retentionpolicy），以实现周期性地删除陈旧的消息
<br><br>1) kafka有两种“保留策略”：
<br><br>a.	根据消息保留的时间，当消息在kafka中保存的时间超过了指定时间，就可以被删除；
<br><br>b.	根据topic存储的数据大小，当topic所占的日志文件大小大于一个阀值，则可以开始删除最旧的消息
<br><br>2) 日志压缩策略
<br><br>在很多场景中，消息的key与value的值之间的对应关系是不断变化的，就像数据库中的数据会不断被修改一样，消费者只关心key对应的最新的value。我们可以开启日志压缩功能，kafka定期将相同key的消息进行合并，只保留最新的value值
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/12.png)
#### 6. 消息可靠性机制
<br>1). 消息发送可靠性
<br><br>生产者发送消息到broker，有三种确认方式（request.required.acks）
<br><br>acks = 0: producer不会等待broker（leader）发送ack 。因为发送消息网络超时或broker crash(1.Partition的Leader还没有commit消息 2.Leader与Follower数据不同步)，既有可能丢失也可能会重发。
<br><br>acks = 1: 当leader接收到消息之后发送ack，丢会重发，丢的概率很小
<br><br>acks = -1: 当所有的follower都同步消息成功后发送ack.  丢失消息可能性比较低。
<br><br>2) 消息存储可靠性
<br><br>每一条消息被发送到broker中，会根据partition规则选择被存储到哪一个partition。如果partition规则设置的合理，所有消息可以均匀分布到不同的partition里，这样就实现了水平扩展。
<br><br>在创建topic时可以指定这个topic对应的partition的数量。在发送一条消息时，可以指定这条消息的key，producer根据这个key和partition机制来判断这个消息发送到哪个partition。
<br><br>kafka的高可靠性的保障来自于另一个叫副本（replication）策略，通过设置副本的相关参数，可以使kafka在性能和可靠性之间做不同的切换。
<br><br>3) 高可靠性的副本
<br><br>sh kafka-topics.sh --create --zookeeper 192.168.11.140:2181 --replication-factor 2 --partitions 3 --topic sixsix
<br><br>--replication-factor表示的副本数
<br><br>4) 副本机制
<br><br>ISR（副本同步队列）
<br><br>维护的是有资格的follower节点
<br><br>a.	副本的所有节点都必须要和zookeeper保持连接状态
<br><br>b.	副本的最后一条消息的offset和leader副本的最后一条消息的offset之间的差值不能超过指定的阀值，这个阀值是可以设置的（replica.lag.max.messages）
#### HW&LEO
<br>关于follower副本同步的过程中，还有两个关键的概念，HW(HighWatermark)和LEO(Log End Offset). 这两个参数跟ISR集合紧密关联。HW标记了一个特殊的offset，当消费者处理消息的时候，只能拉去到HW之前的消息，HW之后的消息对消费者来说是不可见的。也就是说，取partition对应ISR中最小的LEO作为HW，consumer最多只能消费到HW所在的位置。每个replica都有HW，leader和follower各自维护更新自己的HW的状态。对于leader新写入的消息，consumer不能立刻消费，leader会等待该消息被所有ISR中的replicas同步更新HW，此时消息才能被consumer消费。这样就保证了如果leader副本损坏，该消息仍然可以从新选举的leader中获取
<br><br>LEO是所有副本都会有的一个offset标记，它指向追加到当前副本的最后一个消息的offset。当生产者向leader副本追加消息的时候，leader副本的LEO标记就会递增；当follower副本成功从leader副本拉去消息并更新到本地的时候，follower副本的LEO就会增加






















