## MQ
#### 异步消息、解耦、流量削峰
### 1、ActiveMQ
<br>1. 什么是MOM
<br>面向消息的中间件，使用消息传送提供者来协调消息传输操作。 MOM需要提供API和管理工具。 客户端调用api。 把消息发送到消息传送提供者指定的目的地
在消息发送之后，客户端会技术执行其他的工作。并且在接收方收到这个消息确认之前。提供者一直保留该消息
<br><br>2. 消息传递
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
<br><br>3. JMS的可靠性机制
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
<br><br>4. ActiveMQ支持的传输协议
<br>client端和broker端的通讯协议
<br>TCP、UDP 、NIO、SSL、Http（s）、vm
<br><br>5. ActiveMQ持久化存储
 <br><br>![持久化存储](https://github.com/gaoyuanyuan2/distributed/blob/master/img/6.png) 
<br><br>6. 丢失的消息
<br>一些consumer连接到broker1、消费broker2上的消息。消息先被broker1从broker2消费掉，然后转发给这些consumers。假设，
转发消息的时候broker1重启了，这些consumers发现brokers1连接失败，通过failover连接到broker2.
但是因为有一部分没有消费的消息被broker2已经分发到broker1上去了，这些消息就好像消失了。除非有消费者重新连接到broker1上来消费。
<br><br>消息回流处理
<br><br>7. 高可用方案
<br><br>1) 通过zookeeper+activemq实现高可用方案
<br>（master/slave模型）
<br><br>2) 通过zookeeper+activemq实现高可用方案
<br>（master/slave模型）
<br><br>3) 基于共享文件系统的主从方案
<br>挂载网络磁盘，将数据文件保存到指定磁盘上即可完成master/slave模式
<br><br>8.源码 
<br>1) 责任链模式


















