## Redis
#### Redis 是一个开源的使用 ANSI C 语言编写、支持网络、可基于内存亦可持久化的日志型、 Key-Value数据库 ，epoll 所以高性能

### 1、用途、应用场景
<br>1.	数据缓存（商品数据、新闻、热点数据）
<br><br>2.	单点登录
<br><br>3.	秒杀、抢购
<br><br>4.	网站访问排名…
<br><br>5.	应用的模块开发

### 2、持久化
<br>1. 	RDB持久化
<br>RDB压缩的二进制文件用于保存和还原Redis服务器所有数据库中的所有键值对数据）。SAVE命令由服务器进程直接保存操作，所有该命令会阻塞服务器。BGSAVE
由子进程执行保存操作，所以该命令不会阻塞服务器。（间隔保存，会丢失数据）
<br><br>2. 	AOF持久化
<br>AOF日志文件通过保存所有修改数据库的命令请求来记录服务器的数据库状态。AOF重写文件体积变小。（每秒同步，每修改同步，不同步）文件大、效率较低。新文件会覆盖旧的文件（定时转移走）。 
#### redis持久化机制
<br>redis提供了两种持久化策略
#### RDB
<br>RDB的持久化策略： 按照规则定时讲内从的数据同步到磁盘
<br>snapshot
<br>redis在指定的情况下会触发快照
<br><br>1.	自己配置的快照规则
<br>save <seconds><changes>
<br>save 900 1  当在900秒内被更改的key的数量大于1的时候，就执行快照
<br>save 300 10
<br>save 60 10000
<br><br>2.	save或者bgsave
<br>save: 执行内存的数据同步到磁盘的操作，这个操作会阻塞客户端的请求
<br>bgsave: 在后台异步执行快照操作，这个操作不会阻塞客户端的请求
<br><br>3.	执行flushall的时候
<br>清除内存的所有数据，只要快照的规则不为空，也就是第一个规则存在。那么redis会执行快照
<br><br>4.	执行复制的时候
#### 快照的实现原理
<br>1：redis使用fork函数复制一份当前进程的副本(子进程)
<br><br>2：父进程继续接收并处理客户端发来的命令，而子进程开始将内存中的数据写入硬盘中的临时文件
<br><br>3：当子进程写入完所有数据后会用该临时文件替换旧的RDB文件，至此，一次快照操作完成。  
<br><br>注意：redis在进行快照的过程中不会修改RDB文件，只有快照结束后才会将旧的文件替换成新的，也就是说任何时候RDB文件都是完整的。 这就使得我们可以通过定时备份RDB文件来实现redis数据库的备份， RDB文件是经过压缩的二进制文件，占用的空间会小于内存中的数据，更加利于传输。
#### RDB的优缺点
<br>1.	使用RDB方式实现持久化，一旦Redis异常退出，就会丢失最后一次快照以后更改的所有数据。这个时候我们就需要根据具体的应用场景，通过组合设置自动快照条件的方式来将可能发生的数据损失控制在能够接受范围。如果数据相对来说比较重要，希望将损失降到最小，则可以使用AOF方式进行持久化
<br><br>2.	RDB可以最大化Redis的性能：父进程在保存RDB文件时唯一要做的就是fork出一个子进程，然后这个子进程就会处理接下来的所有保存工作，父进程无序执行任何磁盘I/O操作。同时这个也是一个缺点，如果数据集比较大的时候，fork可以能比较耗时，造成服务器在一段时间内停止处理客户端的请求；
#### 实践
<br>修改redis.conf中的appendonly yes ; 重启后执行对数据的变更命令， 会在bin目录下生成对应的.aof文件， aof文件中会记录所有的操作命令
<br>如下两个参数可以去对aof文件做优化
<br>auto-aof-rewrite-percentage 100表示当前aof文件大小超过上一次aof文件大小的百分之多少的时候会进行重写。如果之前没有重写过，以启动时aof文件大小为准
<br>auto-aof-rewrite-min-size 64mb限制允许重写最小aof文件大小，也就是文件大小小于64mb的时候，不需要进行优化
#### AOF
<br>AOF可以将Redis执行的每一条写命令追加到硬盘文件中，这一过程显然会降低Redis的性能，但大部分情况下这个影响是能够接受的，另外使用较快的硬盘可以提高AOF的性能
#### 实践
<br>默认情况下Redis没有开启AOF（appendonlyfile）方式的持久化，可以通过appendonly参数启用，在redis.conf中找到 appendonly yes
<br>开启AOF持久化后每执行一条会更改Redis中的数据的命令后，Redis就会将该命令写入硬盘中的AOF文件。AOF文件的保存位置和RDB文件的位置相同，都是通过dir参数设置的，默认的文件名是apendonly.aof. 可以在redis.conf中的属性 appendfilename appendonlyh.aof修改
#### aof重写的原理
<br>Redis 可以在 AOF 文件体积变得过大时，自动地在后台对 AOF 进行重写： 重写后的新 AOF 文件包含了恢复当前数据集所需的最小命令集合。 整个重写操作是绝对安全的，因为 Redis 在创建新 AOF 文件的过程中，会继续将命令追加到现有的 AOF 文件里面，即使重写过程中发生停机，现有的 AOF 文件也不会丢失。 而一旦新 AOF 文件创建完毕，Redis 就会从旧 AOF 文件切换到新 AOF 文件，并开始对新 AOF 文件进行追加操作。AOF 文件有序地保存了对数据库执行的所有写入操作， 这些写入操作以 Redis 协议的格式保存， 因此 AOF 文件的内容非常容易被人读懂， 对文件进行分析（parse）也很轻松
####  同步磁盘数据
<br>redis每次更改数据的时候， aof机制都会讲命令记录到aof文件，但是实际上由于操作系统的缓存机制，数据并没有实时写入到硬盘，而是进入硬盘缓存。再通过硬盘缓存机制去刷新到保存到文件
<br>appendfsync always每次执行写入都会进行同步 ， 这个是最安全但是是效率比较低的方式
<br>appendfsync everysec每一秒执行
<br>appendfsync no不主动进行同步操作，由操作系统去执行，这个是最快但是最不安全的方式
#### aof文件损坏以后如何修复
<br>服务器可能在程序正在对 AOF 文件进行写入时停机， 如果停机造成了 AOF 文件出错（corrupt）， 那么 Redis 在重启时会拒绝载入这个 AOF 文件， 从而确保数据的一致性不会被破坏。
<br>当发生这种情况时， 可以用以下方法来修复出错的 AOF 文件：
<br><br>1.	为现有的 AOF 文件创建一个备份。
<br><br>2.	使用 Redis 附带的 redis-check-aof 程序，对原来的 AOF 文件进行修复。
<br><br>redis-check-aof --fix
<br>重启 Redis 服务器，等待服务器载入修复后的 AOF 文件，并进行数据恢复。
#### RDB 和 AOF ,如何选择
<br>一般来说,如果对数据的安全性要求非常高的话，应该同时使用两种持久化功能。如果可以承受数分钟以内的数据丢失，那么可以只使用 RDB 持久化。有很多用户都只使用 AOF 持久化， 但并不推荐这种方式： 因为定时生成 RDB 快照（snapshot）非常便于进行数据库备份， 并且 RDB 恢复数据集的速度也要比 AOF 恢复的速度要快 。
<br>两种持久化策略可以同时使用，也可以使用其中一种。如果同时使用的话， 那么Redis重启时，会优先使用AOF文件来还原数据
<br><br>密码设置
<br>requirepass foobaredbind

### 3、原理
<br>1.	链表:实现Redis的各种功能,比如列表键、发布与订阅、满查询、监视器。
<br><br>2.	字典：使用哈希表作为底层实现。字典被用作数据库（哈希键）的底层实现。
<br><br>3.	解决键冲突： Redis的哈希表使用链地址法来解决键冲突，每个哈希表节点都有一个next指针，多个哈希表节点可以用next指针构成一个单向链表，被分配到同一个索引上的多个节点可以用这个单向链表连接起来，这样就解决了键冲突问题。
<br><br>4.	跳跃表是有序集合的底层实现之一。
<br><br>5.	整数集合是集合键的底层实现之一。（底层实现为数组）
<br><br>6.	压缩列表是列表键和哈希键的底层实现之一。
<br><br>7.	Redis的对象系统实现了基于引用计数的内存回收机制。
<br><br>8. 	Redis共有字符串、列表、哈希、集合、有序集合五种类型的对象。当一个对象不再被使用时，该对象所占用的内存就会自动释放。
<br><br>9.	设置键的生存时间或过期时间。
<br><br>10.	当主服务器删除一个过期键之后，他会向所有从服务器发送一条DEL命令，显式地删除过期键。当Redis命令对数据库进行修改之后，服务器会根据配置项客户端发送数据库通知。
<br><br>11.	Redis服务器是一个事件驱动程序，服务器处理的事件分时间事件和文件事件两类。
<br><br>12.	客户端：Redis服务器是典型的一对多服务器程序：通过使用I/O多路复用技术实现文件事件处理器。

### 4、发布和订阅
<br>由PUBLISH,SUBSCRIBE,PSUBSCRIBE命令组成。
<br>发送到频道，订阅频道（PUBLISH <channel><message>）。
<br><br>Redis实现消息队列原理
<br>发布者和订阅者模式：发布者发送消息到队列，每个订阅者都能收到一样的消息。 
<br>生产者和消费者模式：生产者将消息放入队列，多个消费者共同监听，谁先抢到资源，谁就从队列中取走消息去处理。注意，每个消息只能最多被一个消费者接收。

### 5、Redis事务
<br>MULTI(队列中、原子化地执行)（发起）
<br>EXEC（当使用WATCH命令时，只有当受监控的键没有被修改时，EXEC命令才会执行事务中的命令，如果事务执行中止，那么EXEC命令就会返回一个Null值）（提交）
<br>DISCARD清除所有先前在一个事务中放入队列的命令，然后恢复正常的连接状态。（回滚）
<br>WATCH等命令来实现事物功能。
<br>运行时错误 事务不回滚
<br><br>MULTI去开启事务
<br>EXEC去执行事务

### 6、redis的安装
<br>1.	下载redis安装包 
<br><br>2.	tar -zxvf 安装包
<br><br>3.	在redis目录下 执行 make
<br><br>4.	可以通过make test测试编译状态
<br><br>5.	make install [prefix=/path]完成安装

### 7、命令
<br>1.  启动停止redis
<br>./redis-server ../redis.conf
<br>./redis-cli shutdown
<br>以后台进程的方式启动，修改redis.conf   daemonize =yes
<br><br>2.  连接到redis的命令
<br>./redis-cli -h 127.0.0.1 -p 6379
<br><br>3.  其他命令说明
<br>Redis-server 启动服务
<br>Redis-cli 访问到redis的控制台
<br>redis-benchmark 性能测试的工具
<br>redis-check-aof aof文件进行检测的工具
<br>redis-check-dump  rdb文件检查工具
<br>redis-sentinel sentinel 服务器配置

### 8、多数据支持
<br>默认支持16个数据库；可以理解为一个命名空间；跟关系型数据库不一样的点
<br><br>1.	redis不支持自定义数据库名词
<br><br>2.	每个数据库不能单独设置授权
<br><br>3.	每个数据库之间并不是完全隔离的。 可以通过flushall命令清空redis实例面的所有数据库中的数据
<br><br>通过 select dbid去选择不同的数据库命名空间 。 dbid的取值范围默认是0-15

### 9、使用入门
<br>1.	获得一个符合匹配规则的键名列表
<br>keys pattern  [? / * /[]]
<br>keys mic:hobby
<br><br>2.	判断一个键是否存在 ， EXISTS key 
<br><br>3.	type key 去获得这个key的数据结构类型

### 10、各种数据结构的使用
####  字符类型
####  散列类型
####  列表类型
####  集合类型
####  有序集合
<br>1.  字符类型
<br>一个字符类型的key默认存储的最大容量是512M
<br>赋值和取值
<br>SETkey  value
<br>GETkey
<br>递增数字
<br>incr key
<br><br>错误的演示
<br>int value= get key;
<br>value =value +1;
<br>set key value;
<br><br>key的设计
<br>对象类型:对象id:对象属性:对象子属性
<br>建议对key进行分类，同步在wiki统一管理
<br>短信重发机制
<br><br>incryby key increment  递增指定的整数
<br>decr key   原子递减
<br>append key value 向指定的key追加字符串
<br>strlenkey  获得key对应的value的长度
<br>mgetkey key..同时获得多个key的value
<br>mset key value  key value  key value …
<br>setnx
<br><br>2.  列表类型
<br>list, 可以存储一个有序的字符串列表
<br>LPUSH/RPUSH： 从左边或者右边push数据
<br>LPUSH/RPUSH key value value …
<br>｛17 20 19 18 16｝
<br><br>llen num  获得列表的长度
<br>lrangekey  start stop   ;  索引可以是负数， -1表示最右边的第一个元素
<br>lrem key count value
<br>lset key index value
<br>LPOP/RPOP : 取数据
<br>应用场景：可以用来做分布式消息队列
<br><br>3.  散列类型
<br>hash key value不支持数据类型的嵌套
<br>比较适合存储对象
<br>person
<br>age  18
<br>sex   男
<br>name mic
<br><br>hset key field value
<br>hget key filed 
<br><br>hmset key filed value [filed value …]一次性设置多个值
<br>hmget key field field …一次性获得多个值
<br>hgetall key获得hash的所有信息，包括key和value
<br>hexists key field 判断字段是否存在。 存在返回1.不存在返回0
<br>hincryby
<br>hsetnx
<br>hdel key field [field …]删除一个或者多个字段
<br><br>4.  集合类型
<br>set跟list不一样的点。集合类型不能存在重复的数据。而且是无序的
<br>saddkey member [member ...]增加数据； 如果value已经存在，则会忽略存在的值，并且返回成功加入的元素的数量
<br>srem key member  删除元素
<br>smemberskey获得所有数据
<br><br>sdiffkey key …对多个集合执行差集运算
<br>sunion对多个集合执行并集操作,同时存在在两个集合里的所有值
<br><br>5.  有序集合
<br>zadd key score member
<br><br>zrange key start stop [withscores] 去获得元素。 withscores是可以获得元素的分数
<br>如果两个元素的score是相同的话，那么根据(0<9<A<Z<a<z) 方式从小到大
<br>网站访问的前10名。

### 11、分布式锁的实现
#### 锁是用来解决什么问题的;
<br>1.	一个进程中的多个线程，多个线程并发访问同一个资源的时候，如何解决线程安全问题。
<br><br>2.	一个分布式架构系统中的两个模块同时去访问一个文件对文件进行读写操作
<br><br>3.	多个应用对同一条数据做修改的时候，如何保证数据的安全性
<br><br>在但进程中，我们可以用到synchronized、lock之类的同步操作去解决，但是对于分布式架构下多进程的情况下，如何做到跨进程的锁。就需要借助一些第三方手段来完成
<br><br>设计一个分布式所需要解决的问题
<br><br>分布式锁的解决方案
<br>1.	怎么去获取锁
#### 数据库，通过唯一约束
```sql
lock(
     id  int(11),
     methodName  varchar(100),
     memo varchar(1000) ,
     modifyTime timestamp,
    unique key mn(method) -- 唯一约束
)
```
<br><br>获取锁的伪代码

```java

try{
    exec  insert into lock(methodName,memo) values(‘method’,’desc’);
    return true;
}Catch(DuplicateException e){
return false;
}

```

<br><br>释放锁

```sql
delete from lock where methodName=’’;
```

##### 存在的需要思考的问题
<br>1.	锁没有失效时间，一旦解锁操作失败，就会导致锁记录一直在数据库中，其他线程无法再获得到锁
<br><br>2.	锁是非阻塞的，数据的insert操作，一旦插入失败就会直接报错。没有获得锁的线程并不会进入排队队列，要想再次获得锁就要再次触发获得锁操作
<br><br>3.	锁是非重入的，同一个线程在没有释放锁之前无法再次获得该锁
#### zookeeper实现分布式锁
<br>利用zookeeper的唯一节点特性或者有序临时节点特性获得最小节点作为锁. zookeeper 的实现相对简单，通过curator客户端，已经对锁的操作进行了封装
<br><br>同时写一个结点，写同一个结点watch

##### zookeeper的优势
<br>1.	可靠性高、实现简单
<br><br>2.	zookeeper因为临时节点的特性，如果因为其他客户端因为异常和zookeeper连接中断了，那么节点会被删除，意味着锁会被自动释放
<br><br>3.	zookeeper本身提供了一套很好的集群方案，比较稳定
<br><br>4.	释放锁操作，会有watch通知机制，也就是服务器端会主动发送消息给客户端这个锁已经被释放了
#### 基于缓存的分布式锁实现
<br>redis中有一个setNx命令，这个命令只有在key不存在的情况下为key设置值。所以可以利用这个特性来实现分布式锁的操作

### 12、redis多路复用机制
<br>linux的内核会把所有外部设备都看作一个文件来操作，对一个文件的读写操作会调用内核提供的系统命令，返回一个 file descriptor（文件描述符）。对于一个socket的读写也会有响应的描述符，称为socketfd(socket描述符)。而IO多路复用是指内核一旦发现进程指定的一个或者多个文件描述符IO条件准备好以后就通知该进程
<br>IO多路复用又称为事件驱动，操作系统提供了一个功能，当某个socket可读或者可写的时候，它会给一个通知。当配合非阻塞socket使用时，只有当系统通知我哪个描述符可读了，我才去执行read操作，可以保证每次read都能读到有效数据。操作系统的功能通过select/pool/epoll/kqueue之类的系统调用函数来使用，这些函数可以同时监视多个描述符的读写就绪情况，这样多个描述符的I/O操作都能在一个线程内并发交替完成，这就叫I/O多路复用，这里的复用指的是同一个线程
<br>多路复用的优势在于用户可以在一个线程内同时处理多个socket的 io请求。达到同一个线程同时处理多个IO请求的目的。而在同步阻塞模型中，必须通过多线程的方式才能达到目的

### 13、redis中使用lua脚本
#### lua脚本
<br>Lua是一个高效的轻量级脚本语言，用标准C语言编写并以源代码形式开放， 其设计目的是为了嵌入应用程序中，从而为应用程序提供灵活的扩展和定制功能
#### 使用脚本的好处
<br>1.	减少网络开销，在Lua脚本中可以把多个命令放在同一个脚本中运行
<br><br>2.	原子操作，redis会将整个脚本作为一个整体执行，中间不会被其他命令插入。换句话说，编写脚本的过程中无需担心会出现竞态条件
<br><br>3.	复用性，客户端发送的脚本会永远存储在redis中，这意味着其他客户端可以复用这一脚本来完成同样的逻辑

### 14、集群
<br>复制（master、slave）

<br><br>节点通过握手来将其他节点添加到自己所处的集群当中。无中心节点
<br>集群中的16384个槽可以分别平均指派给集群中的各个节点，每个节点都会记录哪些槽指派给了自己，而哪些槽又被指派给了其他节点，
<br>集群里的从节点用于复制主节点，并在主节点下线时，代替主节点继续处理命令请求。
<br>集群中的节点通过发送和接收消息来进行通信，常见的消息包括MEET、PING、PONG、PUBLISH、FAIL。
 
#### 配置过程
<br>修改11.140和11.141的redis.conf文件，增加slaveof masterip masterport
<br>slaveof192.168.11.138 6379
#### 实现原理
<br><br>1.	slave第一次或者重连到master上以后，会向master发送一个SYNC的命令
<br><br>2.	master收到SYNC的时候，会做两件事
<br><br>a)	执行bgsave（rdb的快照文件）
<br><br>b)	master会把新收到的修改命令存入到缓冲区
#### 缺点
<br>没有办法对master进行动态选举
<br>复制的方式
<br><br>1.	基于rdb文件的复制（第一次连接或者重连的时候）
<br><br>2.	无硬盘复制
<br><br>3.	增量复制
<br><br>PSYNC master run id. offset
#### 哨兵机制
<br>是Redis高可用性的解决方案。一般10s每次的频率向被监视的主服务器和从服务器发送INFO命令，当主服务器处于下线状态，或者Sentinel
正在对主服务器进行故障转移操作是，发送频率会改成每秒一次。每个主节点一个从节点，故障从提主。自动。基数防脑裂。
<br><br>sentinel
<br><br>1.	监控master和salve是否正常运行
<br><br>2.	如果master出现故障，那么会把其中一台salve数据升级为master
<br><br>集群（redis3.0以后的功能）
<br>根据key的hash值取模 服务器的数量 。 
<br>hash
#### 集群的原理
<br>Redis Cluster中，Sharding采用slot(槽)的概念，一共分成16384个槽，这有点儿类似前面讲的pre sharding思路。对于每个进入Redis的键值对，根据key进行散列，分配到这16384个slot中的某一个中。使用的hash算法也比较简单，就是CRC16后16384取模。Redis集群中的每个node(节点)负责分摊这16384个slot中的一部分，也就是说，每个slot都对应一个node负责处理。当动态添加或减少node节点时，需要将16384个槽做个再分配，槽中的键值也要迁移。当然，这一过程，在目前实现中，还处于半自动状态，需要人工介入。Redis集群，要保证16384个槽对应的node都正常工作，如果某个node发生故障，那它负责的slots也就失效，整个集群将不能工作。为了增加集群的可访问性，官方推荐的方案是将node配置成主从结构，即一个master主节点，挂n个slave从节点。这时，如果主节点失效，Redis Cluster会根据选举算法从slave节点中选择一个上升为主节点，整个集群继续对外提供服务。这非常类似服务器节点通过Sentinel监控架构成主从结构，只是Redis Cluster本身提供了故障转移容错的能力。
<br><br>slot（槽）的概念，在redis集群中一共会有16384个槽，
<br>根据key的CRC16算法，得到的结果再对16384进行取模。 假如有3个节点
<br>node1  0 5460
<br>node2  5461 10922
<br>node3  10923 16383
<br>节点新增
<br>node4  0-1364,5461-6826,10923-12287
<br>删除节点
<br>先将节点的数据移动到其他节点上，然后才能执行删除
#### 市面上提供了集群方案
<br>1.	redis shardding   而且jedis客户端就支持shardding操作SharddingJedis； 增加和减少节点的问题； pre shardding
<br>3台虚拟机 redis。但是我部署了9个节点 。每一台部署3个redis增加cpu的利用率
<br>9台虚拟机单独拆分到9台服务器
<br><br>2.	codis基于redis2.8.13分支开发了一个codis-server
<br><br>3.	twemproxy  twitter提供的开源解决方案

### 15、缓存更新
redis缓存的更新（2pc 3pc 强一致，但是效率低）
1.  先删除缓存，再更新数据库(并发操作更新还没有完成，就查询，load之前错误数据到缓存，之后数据都会出现脏读)
2.  先更新数据库，更新成功后，让缓存失效（也会出现脏读，概率低一些）
3.  更新数据的时候，只更新缓存，不更新数据库，然后同步异步调度去批量更新数据库（性能高，数据不是强一致）

### 16、穿透、击穿
1.  缓存穿透 布隆过滤器
2.  缓存击穿 同时失效（随机时间） 互斥锁让请求排队等待

































































