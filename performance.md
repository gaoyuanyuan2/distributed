# 性能优化

### QPS: 每秒钟处理完请求的次数

### TPS：每秒钟处理完的事务次数

[Java内存和CPU监视工具和技术](https://dzone.com/articles/java-memory-and-cpu-monitoring-tools-and-technique)

[内存泄漏](https://dzone.com/articles/how-memory-leaks-happen-in-java-apps)

[如何阅读线程转储](https://dzone.com/articles/how-to-read-a-thread-dump)

## CPU

1.  top  查看进程

* 按1 可以看cpu信息

* 按shift + h 看线程


2.  top -H -p  17343 检查进程内运行的线程


3.  cat /proc/cpuinfo 查看cpu


http://man7.org/linux/man-pages/man1/top.1.html


4. 查找线程一直占用cpu的问题

* curl localhost:8080/hello

* cpu 都被占了，很卡，但是能用。因为会切换。

* jstack 38983 > a.txt （线程tack）

* printf "%x \n " 12455线程号转16进制  查询线程号即可

* grep -A 5 12af a.txt

* 简写：jstack 38983 | grep '12af' -C5 --color


4.  vmstat

* vmstat 1 每秒看cpu


http://www.man7.org/linux/man-pages/man8/vmstat.8.html


### 小结

1.如果CPU使用率上升，就需要参考性能分析信息，弄清楚是什么花费了这么多时间。如果GC时间变长，就该查阅堆性能分析信息，搞明白是什么消耗了这么多内存。

如果CPU和GC时间都减少，某些地方的竞争可能降低了性能:栈数据可以指示特定的同步瓶颈。

2.检查应用性能时，首先应该审查CPU时间。

3.优化代码的目的是提升而不是降低(更短时间段内的) CPU使用率。
  
4.在试图深入优化应用前，应该先弄清楚为何CPU使用率低。
  
## Memory

* free -g 看内存g

* free -m 看内存m
* df -h james/ 查看文件大小， -h 人能看懂的
* df -h 查看整个文件系统占有率

http://www.man7.org/linux/man-pages/man1/free.1.html

## IO

* iostat 1

* iostat -dx 1

* %util io cup占用多少百分比做io操作


http://www.man7.org/linux/man-pages/man1/iostat.1.html

### 小结
1.对于所有应用来说，监控磁盘使用率非常重要。即便不直接写磁盘的应用，系统交换仍然会影响它们的性能。

2.写入磁盘的应用遇到瓶颈，是因为写人数据的效率不高(吞吐量太低),或者是因为写人太多数据(吞吐量太高)。

## 4、NetWork

* nicstat 查看网卡

## 5、测试

1.  秒杀 js过滤99%、Nginx过滤99% 


2.  业务梳理比技术好


3.  99% 都是数据库问题


3.  AB局部更新，防压力


4.  push


5.  能沟通解决问题就不用用技术


## JVM（JDK>JRE>JVM）

1.  软件层面机器码的翻译


2.  内存管理


3.  永久代溢出，Meta Space 自动扩容


4.  对象生命周期不一样所以分代

### 栈

1.栈描述的是方法执行的内存模型。每个方法被调用都会创建一个栈帧(存储局部变量、操作数、方法出口等)


2.JVM为每个线程创建一个栈 ,用于存放该线程执行方法的信息(实际参数、局部变量等)


3.栈属于线程私有，不能实现线程间的共享!


4.栈的存储特性是“先进后出,后进先出”


5.栈是由系统自动分配,速度快!栈是一个连续的内存空间! 

动态链接：方法调用。

### 堆

堆的特点如下: 

* 1.堆用于存储创建好的对象和数组(数组也是对象)

* 2.JVM只有一个堆，被所有线程共享

* 3.堆是一个不连续的内存空间,分配灵活,速度慢!

* GC管理的主要区域，基本采用分代收集算法。

### 方法区

方法区(又叫静态区)特点如下:

* 1.JVM只有一个方法区，被所有线程共享!

* 2.方法区实际也是堆，只是用于存储类、常量相关的信息!

* 3.用来存放程序中永远是不变或唯一的内容。 (类信息[Class对象]、静态变量、字符串常量等)

### 执行引擎

最核心的组件，它负责执行虚拟机的字节码。一般会先编译成机器码后执行。

### 直接内存

并不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范定义的内存区域,
但这部分内存也被频繁的使用。NIO引入一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，
它可以使用Native函数库直接分配堆外内存，提高性能,避免了在Java堆和Native堆中来回复制数据。

#### 程序计数器 （多线程切换）

是一块较小的内存空间，它可以看作是当前线程所执行的字节码的行号指示器。在虚拟机的概念模型里，
字节码解释器工作时候是通过改变这个计数器的值来选取下一条需要执行的字节码指令、分支、循环、跳转、异常处理、
线程恢复等基础功能都需要依赖这个计数器来完成。


![JVM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/27.png) 


![JMM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/28.png) 

### 什么样的对象需要被GC

1.  引用计数法

这种靠引用计数的方式不太靠谱:假设有一个对象链接列表，列表中的每一个对象(除了头节点)都指向列表中的另一个对象，但是，如果没有任何一个引用指向列表头，这个列表就没人使用，
可以被垃圾回收器回收。如果这是一个循环列表(即列表的尾元素反过来又指向了头元素)，那么列表中的每一-个元素都包含一个引用，
即使这个列表内没有任何一个对象实际被使用，因为没有任何一个对象指向这个列表。

所以引用是无法通过计数的方式动态跟踪的; JVM必须定期地描堆来查找不再使用的对象。一旦发现垃圾对象，JVM
会回收这些对象所持有的内存，把它们分配给需要内存的其他对象。然而，简单地记录空闲内存也无法保证将来有足够的可用内存，有些时候，我们还必须进行内存整理来防止内存碎片。

2.  可达性分析GCRoots （解决相互引用无法回收的问题）

* 虚拟机栈中本地变量表引用的对象方法区中

* 类静态变量引用的对象常量引用的对象

* 本地方法栈中JNI引用的对象


3.  不可达是不是就一定会被回收

* finalize()
    

![回收算法](https://github.com/gaoyuanyuan2/distributed/blob/master/img/29.png) 


### 参数说明

-Xms20M  starting (堆的起始大小)


-Xmx     max  (堆的最大大小)


-Xmn     new  (堆的新生大小)


1. 对象分配eden

* -XX:SurvivorRatio=8

* 8:1:1


TLAB  Thread Local Allaction Buffer
(栈上分配，不存在并发，减少并发争夺内存，避免锁)


2.  对象很大

* -XX:PretenureSizeThreshold=3145728   3M


3.  长期存活的对象 

* -XX:MaxTenuringThreshold=15


4.  动态对象年龄判定

* 相同年龄所有对象的大小总和 > Survivor空间的一半
	

5.  分配担保

* Minor GC 之前检查 老年代最大可用连续空间是否>新生代所有对象总空间


Minor GC  

Major GC

Full  GC
	
### 什么样的对象需要回收？

### 引用

* 强  Object object = new Object();

* 软  (全局变量，优化缓存，value软引用（内存不够，下一次gc会被回收）)

* 弱  

* 虚  (引用会被通知)
	
### 回收

1.  方法论

* 标记-清除算法

* 复制回收算法（新生代）

* 标记-整理算法


2.  垃圾收集器（对算法的实现）

STW  Stop The World

Serial

ParNew 

   * -XX:ParallelGCThreads
   
Parallel Scavenge （全局）

* 吞吐量 = 运行用户代码时间 / （运行用户代码时间  + 垃圾收集时间）

* -XX:MaxGCPauseMillis=n

* -XX:GCTimeRatio=n

* -XX:UseAdaptiveSizePolicy   GC  Ergonomics （old区长期高于某个比例，会触发能效gc）

Serial Old

* CMS备用预案  Concurrent Mode Failusre时使用（full gc 一直回收不掉，走这个）

* 标记-整理算法

Parallel Old

* 标记-整理算法

CMS

* 标记-清除算法

* 减少回收停顿时间

* 碎片 -XX:CMSInitiationgOccupancyFraction  

* Concurrent Mode Failure 启用Serial Old

-XX:+UseCMSCompactAtFullCollection

* -XX:CMSFullGCsBeforeCompaction 执行多少次不压缩FullGC后 来一次带压缩的 0 表示每次都压

* -XX:+UseConcMarkSweep

G1

回收的时间节点
	
如何查看当前的垃圾回收器

* -XX:+PrintFlagsFinal

* -XX:+PrintCommandLineFlags

* server client

* MBean
	
GC日志

1.输出日志

* -XX:+PrintGCTimeStamps 

* -XX:+PrintGCDetails 

* -Xloggc:/home/administrator/james/gc.log

* -XX:+PrintHeapAtGC


2.日志文件控制

* -XX:-UseGCLogFileRotation

* -XX:GCLogFileSize=8K


3.怎么看
	

JDK自带的 监控工具

* https://docs.oracle.com/javase/8/docs/technotes/tools/windows/toc.html

	* jmap -heap pid 堆使用情况
	
	* jstat  -gcutil pid 1000
	
	* jstack  线程dump 
	
	* jvisualvm
	
	* jconsole
	
	* gc.log
	
MAT

	 http://help.eclipse.org/oxygen/index.jsp?topic=/org.eclipse.mat.ui.help/welcome.html
	
	 -XX:+HeapDumpOnOutOfMemoryError 
	
	 -XX:HeapDumpPath=/home/administrator/james/error.hprof
	

怀疑：

	1.看GC日志  126719K->126719K(126720K)
	
	2.dump
	
	3.MAT
	
		* 1.占用Retained Heap
		
		* 2.看有没有GC Root指向
		
	 deadlock BLOCKED
	 
 jps 查看进程号
 
 kill 进程号	


![JVM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/47.jpg) 


![JMM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/48.jpg) 


VM参数

 http://www.oracle.com/technetwork/java/javase/tech/vmoptions-jsp-140102.html


安全点

GC优化	 

https://mp.weixin.qq.com/s/t1Cx1n6irN1RWG8HQyHU2w

swap  https://blog.csdn.net/u010602357/article/details/54286346?tdsourcetag=s_pctim_aiomsg

 MAT  http://www.eclipse.org/mat/

https://github.com/oldmanpushcart/greys-anatomy

https://note.youdao.com/share/?id=80df2676169cb223861869fb2a6017e5&type=note#/

### 调优

#### 1

确实没有什么好的机制可以算出程序所需要的代码缓存。所以，如何增加代码缓存，基本上就是摸着石头过河，通常的做法是简单地增加1倍或3倍。

 -XX:ReservedCodeCacheSize=N (对特定编译器来说，N为默认的值)标志可以设置代码缓存的最大值。
 
 代码缓存的管理和大多数JVM内存一样，有初始值(由-XX:InitialCodeCacheSize=N指定)。
 
 代码缓存从初始大小开始分配，一且充满就会增加(直至最大值)。代码缓存的初始大小依据芯片架构和所用的JVM编译器而有所不同(例如Intel机器的client编译器的初始代码缓存为160KB,server编译器的初始代码缓存为2496 KB)。
 
 缓存大小的自动调整在后台进行，不会对性能造成实际影响，所以通常只需要设定ReservedCodeCacheSize (也就是设定代码缓存的最大值)。

为了永远不超出空间而将代码缓存的最大值设得很大，这有没有什么坏处?这取决于目标机器上有多少可用资源。

代码缓存设为1GB, JVM就会保留1GB的本地内存空间。虽然这部分内存在需要时才会分配，但它仍然是被保留的，这意味着为了满足保留内存，你的机器必须有足够的虚拟内存。

*小结1*

1. 当方法和循环执行次数达到某个阈值的时候，就会发生编译；

2. 改变阈值会导致代码提早或推后编译；

3. 由于计数器会随着时间而减少，以至于“温热”的方法可能永远都达不到编译的阈值(特 别是对server编译器来说)。

*小结2*

1. 观察代码如何被编译的最好方法是开启PrintCompilation；

2. PrintCompilation 开启后所输出的信息可用来确认编译是否和预期一样。

#### 2

现代JVM的类型繁多，最主流的四个垃圾收集器分别是: Serial 收集器(常用于单CPU环境)、Throughput (或者Parallel)收集器，Concurrent 收集器(CMS) 和G1收集器。

#### 3

深入到这些具体实现似乎太过于琐屑，但垃圾收集的性能就是由这些基本操作所决定的:找到不再使用的对象、回收它们使用的内存、对堆的内存布局进行压缩整理。

完成这些操作时不同的收集器采用了不同的方法，这也是不同垃圾收集器表现出不同性能特征的原因。

#### 4
所有应用线程都停止运行所产生的停顿被称为时空停顿(stop-the-world)。 通常这些停顿对应用的性能影响最大，调优垃圾收集时，尽量减少这种停顿是最为关键的考量因素。

#### 5
Java中，这种操作是非常普遍的，所以垃圾收集器设计时就特别考虑要处理大量(有时候是大多数)的临时对象，这也是分代设计的初衷之一。新生代是堆的一部分，对象首先在新生代中分配。
新生代填满时，垃圾收集器会暂停所有的应用线程，回收新生代空间。不再使用的对象会被回收，仍然在使用的对象会被移动到其他地方。这种操作被称为Minor GC。

采用这种设计有两个性能上的优势。其一，由于新生代仅是堆的一部分，与处理整个堆相比，处理新生代的速度更快。而这意味着应用线程停顿的时间会更短。你可能也看到了这其中的权衡，
这意味着应用程序线程会更频繁地发生停顿，因为JVM不再等到整个堆都填满才进行垃圾收集;本章后续部分会针对其利弊进行深入的讨论。然而，就目前而言，更短的停顿显然能带来更多的优势，即使发生的频率更高。

对象不断地被移动到老年代，最终老年代也会被填满，JVM需要找出老年代中不再使用的对象，并对它们进行回收。而这便是垃圾收集算法差异最大的地方。
简单的垃圾收集算法直接亭掉所有的应用线程。找出不再使用的对象，对其进行回收，接着对堆空间进行整理。这个过程被称为Full GC,通常导致应用程序线程长时间的停顿。

另一方面，通过更复杂的计算，我们还有可能在应用线程运行的同时找出不再使用的对象;CMS和G1收集器就是通过这种方式进行垃圾收集的。由于它们不需要停止应用线程就能找出不再用的对象，CMS和G1收集器被称为Concurrent垃圾收集器。同时，由于它们将停止应用程序的可能降到了最小，也被称为低停顿(Low-Pause) 收集器(有时也称为无停顿收集器，虽然这个叫法相当不确切)。Concurrent 收集器也使用各种不同的方法对老年代空间进行压缩。

使用CMS或G1收集器时，应用程序经历的停顿会更少(也更短)。其代价是应用程序会消耗更多的CPU。CMS和G1收集也可能遭遇长时间的FullGC停顿(尽量避免发生那样的停顿是这些调优算法要考虑的重要方面)。

#### 6

单个请求会受停顿时间的影响一-不过 其受Full GC长时间停顿的影响更大。如果目标是要尽可能地缩短响应时间，那么选择使用Concurrent收集器更合适。

如果平均响应时间比最大响应时间更重要(譬如90%的响应时间)，采用Throughput收集器通常就能满足要求。

使用Concurrent收集器来避免长的停顿时间也有其代价，这会消耗额外的CPU。类似地，为批量应用选择垃圾收集器可以遵循下面的原则。

如果CPU足够强劲，使用Concurrent收集器避免发生FullGC停顿可以让任务运行得更快。

如果CPU有限，那么Concurrent收集器额外的CPU消耗会让批量任务消耗更多的时间。

快速小结

1.所有的GC算法都将堆划分成了老年代和新生代。

2.所有的GC算法在清理新生代对象时，都使用了“时空停顿”(stop-the-world)方式的垃圾收集方法，通常这是-一个能较快完成的操作。

#### 7 Throughput垃圾收集器

Throughput收集器是Server级虚拟机(多CPU的Unix机器以及任何64位虚拟机)的默认收集器。

Throughput收集器使用多线程回收新生代空间，MinorGC的速度比使用Serial收集器快得多。处理老年代时Throughput收集器也能使用多线程方式。
这已经是JDK 7u4及之后的版本的默认行为，对于之前老版本的JDK 7虚拟机，通过-XX: +UseParallel0ldCC标志可以开启这个功能。由于Throughput收集器使用多线程，Throughput,收集器也常常被称为Parallel收集器。

Throughput收集器在MinorGC和FullGC时会暂停所有的应用线程，同时在FullGC过程中会对老年代空间进行压缩整理。由于在大多数适用的场景，它已经是默认的收集器，所以你基本上不需要显式地启用它。
如果需要，可以使用-XX: +UseParallelGC、-XX:+UseParalle10ldCC 标志启用Throughput收集器。

#### 8 CMS收集器
     
CMS收集器设计的初衷是为了消除Throughput收集器和Serial收集器FullGC周期中的长时间停顿。
CMS收集器在MinorGC时会暂停所有的应用线程，并以多线程的方式进行垃圾回收。然而，这其中最显著的不同是，CMS不再使用Throughput的收集算法(-xX:+UseParallelGC)，改用新的算法来收集新生代对象使 用-XX:+UseParNewGC标志)。
CMS收集器在Full GC时不再暂停应用线程，而是使用若千个后台线程定期地对老年代空间进行扫描，及时回收其中不再使用的对象。
这种算法帮助CMS成为一个低延迟的收集器:应用线程只在MinorGC以及后台线程扫描老年代时发生极其短暂的停顿。
应用程序线程停顿的总时长与使用Throughput收集器比起来短得多。
     
这里额外付出的代价是更高的CPU使用必须有足够的CPU资源用于运行后台的垃圾收集线程，在应用程序线程运行的同时扫描堆的使用情况。
除此之外，后台线程不再进行任何压缩整理的工作，这意味着堆会逐渐变得碎片化。

如果CMS的后台线程无法获得完成他们任务所需的CPU资源，或者如果堆变得过度碎片化以至于无法找到连续空间分配对象, CMS就蜕化到Serial收集器的行为:暂停所有应用线程，使用单线程回收、整理老年代空间。
这之后又恢复到并发运行，再次启动后台线程(直到下一次堆变得过度碎片化)。通过-KX: +UseConcMarkSweepGC、-XX:+UseParNewGC 标志(默认情况下，这两个标志都是禁用的)可以启用CMS垃圾收集器。


Java也提供了一种机制让应用程序强制进行GC:这就是System.gc()方法。通常情况下，试图通过调用这个方法显式触发GC都不是个好主意。
调用这个方法会触发FullGC (即使JVM使用CMS或者G1垃圾收集器)，应用程序线程会因此而停顿相当长的一段时间。同时，调用这个方法也不会让应用程序更高效，它会让GC更早地开始，但那实际只是将性能的影响往后推迟而已。

任何原则都有例外，尤其是在做性能监控或者基准测试时。运行少量的代码进行基准测试时，为了更快地预热JVM，在测量周期之前强制进行一次GC还是有意义的类似的情况还包括在进行堆分析时，
通常在获取堆转储之前，强制进行一次Full GC是一个不错的主意 虽然大多数抓取堆转储的方法都能进行 FullGC，也存在其他的方法
可以强制进行Full GC:你可以通过执行jcmd<进程号> GC.run,或者使用jconsole连接到JVM在内存面板上单击“进行GC按钮。

如果你运行的程序调用的第三方代码中错误地调用了System.gc()方法，可以通过JVM参数-XX:+DisableExplicitGc显式地禁止这种类型的GC;默认情况下该标志是关闭的。


#### 9. G1垃圾收集器

G1垃圾收集器(或者垃圾优先收集器)的设计初衷是为了尽量缩短处理超大堆(大于4GB) 时产生的停顿。G1收集算法将堆划分为若千个区域(Region)， 不过它依旧属于分代收集器。
这些区域中的一部分包含新生代，新生代的垃圾收集仍然采用暂停所有应用线程的方式，将存活对象移动到老年代或者Survivor空间。同其他的收集算法一样，这些操作也利用多线程的方式完成。

G1收集器属于Concurrent收集器:老年代的垃圾收集工作由后台线程完成，大多数的工作不需要暂停应用线程。由于老年代被划分到不同的区域，G1收集器通过将对象从一个区域复制到另一个区域，完成对象的清理工作，这也意味着在正常的处理过程中，G1收集器实现了堆的压缩整理(至少是部分的整理)。
因此，使用G1收集器的堆不大容易发生碎片化----虽然这种问题无法避免。

同CMS收集器一样，避免FullGC的代价是消耗额外的CPU周期:负责垃圾收集的多个后台线程必须能在应用线程运行的同时获得足够的CPU运行周期。通过标志  - XX:+UseG1GC(默认值是关闭的)可以启动G1垃圾收集器。


####  快速小结

1.这四种垃圾收集算法分别采用了的不同的方法来缓解GC对应用程序的影响。

2.Serial收集器常用于仅有单CPU可用以及当其他程序会干扰GC的情况(通 常是默认值)。

3.Throughput收集器在其他的虚拟机上是默认值，它能最大化应用程序的总吞吐量，但是有些操作可能遭遇较长的停顿。

4.CMS收集器能够在应用线程运行的同时并行地对老年代的垃圾进行收集。如果CPU的计算能力足以支撑后台垃圾收集线程的运行，该算法能避免应用程序发生Full GC。

5.G1收集器也能在应用线程运行的同时并发地对老年代的垃圾进行收集，在某种程度上能够减少发生FullGC的风险。G1的设计理念使得它比CMS更不容易遭遇Full GC。

#### 10 Serial收集器
Serial收集器最适用于应用程序的内存使用少于100MB的场景。这种情况下应用程序只需要很小的堆，无论是Throughput收集器的并行收集，还是CMS收集器或G1收集器的后台收集都发挥不了太大的作用。

##### 采用不同的GC算法进行批量处理的时间消耗

|垃圾收集算法  |4核CPU ( CPU利用率)|  1核CPU ( CPU利用率)|
|:------------:|:--:|:--:|
|CMS  |78.09 (30.7%)  |120.0 (100%)|
|Throughput  |81.00 (27.7%)  |111.6 (100%)|

如果有额外的CPU处理能力(这很可能是个问题)，那么使用Concurrent收集器将极大地提升应用程序的性能。这里的关键在于我们能否提供足够的CPU给Concurrent收集器的线程进行后台的处理工作。举个简单的例子: 一个单CPU的机器上，单线程的应用程序已经消耗了100%的CPU资源。该应用程序使用Throughput收集器运行时，GC间歇性地发生，导致应用程序线程出现停顿。同样的程序，如果切换到Concurrent收集器，操作系统- .会在CPU.上运行应用程序线程，一会儿运行GC的后台线程。最终的结果是一样的:操作系统运行其他线程时，应用程序线程依然会发生停顿(不过有可能是更短时间的停顿)。

#### 快速小结
1.使用Throughput收集器处理应用程序线程的批量任务能最大程度地利用CPU的处理能力，通常能获得更好的性能。

2. 如果批量任务并没有使用机器上所有可用的CPU资源，那么切换到Concurrent收集器往往能取得更好的性能。


#### 快速小结

1.衡量标准是响应时间或吞吐量，在Throughput收集器和Concurrent收集器之间做选择的依据主要是有多少空闲CPU资源能用于运行后台的并发线程。

2.通常情况下，Throughput收集器的平均响应时间比Concurrent收集器要差，但是在90%响应时间或者99%响应时间这几项指标上, Throughput收集器比Concurrent收集器要好一一些。

3.使用Throughput收集器会超负荷地进行大量FullGC时，切换到Concurrent收集器通常能获得更低的响应时间。

4. CMS收集器和G1收集器之间的抉择

#### 11
一般情况下，堆空间小于4 GB时，CMS收集器的性能比G1收集器好。CMS收集器使用的算法比GT更简单，

因此在比较简单的环境中( 譬如堆的容量很小的情况)，它运行得更快。使用大型堆或巨型堆时，由于G1收集器可以分割工作，通常它比CMS收集器表现更好。


G1收集器采用了不同的方式来处理这个问题，它将老年代划分成不同的区域(Region)，能更加容易地使用多个线程分担扫描老年代空间的任务。

如果后台线程跟不上处理的速度，G1收集器也会发生并发模式失效，但是G1算法已经使得发生这种状况的几率减小了很多。

由于CMS收集器不对堆进行压缩整理(除非发生了耗时的FullGC)，堆的碎片化也会触发CMS收集器进行FullGC。G1算法在处理过程中随时进行着堆的压缩整理，不过G1收集器依然可能遭遇堆的碎片化问题，但是与CMS收集器比较起来，它的设计让它又领先了一步。

调优CMS收集器和G1收集器避免发生这些失效的方法很多，但对一些应用程序来说却不一定奏效。随着堆的不断增大(发生Full GC的代价变得更加昂贵)，使用G1收集器更易于避免这些问题的发生。
(另一方面，对有的程序，试图通过调优这两种收集器的任何一种避免发生并发模式失效几乎是不可能的任务。因此，即使应用程序的性能目标似乎与Concurrent收集器保持致，使用Throughput收集器却可能是更明智的选择)。


最后，在这三种收集器的选择时还有- .些微妙的无形因素需要考虑。Throughput 收集器是这三个收集器中年代最久远的一个，这意味着JVM工程师们已经花费了大量的时间精力雕琢把玩它，它的习性也更为大家所熟知。
G1作为相对较新的一种垃圾收集算法，更容易碰到设计时无法预期的极端情况。相对而言，G1算法中影响性能的调优控制开关更少，这可能是好事，也可能是坏事。直到Java 7u4, G1都一直被当作实验版本，它的一些调优特性直到Java 7u10中才提供出来。
相对于Java 7及之前的版本而言，G1的性能提升主要体现在Java8中。G1将来的工作可能会关注在如何提高它在较小的堆上相对于CMS的性能优势。

#### 快速小结

1.选择Concurrent收集器时，如果堆较小，推荐使用CMS收集器。

2. G1的设计使得它能够在不同的分区(Region) 处理堆，因此它的扩展性更好，比CMS更易于处理超大堆的情况。

与其他的性能问题一样，选择堆的大小其实是一种平衡。如果分配的堆过于小，程序的大部分时间可能都消耗在GC上，没有足够的时间去运行应用程序的逻辑。
但是，简单粗暴地设置一个特别大的堆也不是解决问题的方法。GC停顿消耗的时间取决于堆的大小，如果增大堆的空间，停顿的持续时间也会变长。这种情况下，停顿的频率会变得更少，但是它们持续的时间会让程序的整体性能变慢。

系统中运行着大量不同的应用程序时，这个流程工作得很顺畅，因为大多数的应用程序不会同时处于活跃状态。但是，对于Java应用，它工作得并不那么好。
如果一个Java应用使用了这个系统上大约12G的堆，操作系统可能在RAM上分配了8G的堆空间另外4 G的空间存在于磁盘(这个假设对实际情况进行了一些简化，因为应用程序也会使用部分的RAM)。
JVM不会了解这些:操作系统完全屏蔽了内存交换的细节。这样，JVM愉快地填满了分配给它的12G堆空间。但这样就导致了严重的性能问题因为操作系统需要将相当一-部分的数据由磁盘交换到内存(这是一个昂贵操作的开始)。

因此，调整堆大小时首要的原则就是永远不要将堆的容量设置得比机器的物理内存还大，另外，如果同一台机器上运行着多个JVM实例，这个原则适用于所有堆的总和。
除此之外，你还需要为JVM自身以及机器上其他的应用程序预留一部分的内存空间:通常情况下，对于普通的操作系统，应该预留至少1 G的内存空间。

#### 默认堆的大小

![默认堆的大小](https://github.com/gaoyuanyuan2/distributed/blob/master/img/69.jpg) 

### 12

个经验法则是完成Full GC后，应该释放出70%的空间

另一方面，如果你确切地了解应用程序需要多大的堆，那么你可以将堆的初始值和最大值直接设置成对应的数值(譬如: -Xms4096m -Xmx4096m)。 这种设置能稍微提高GC的运行效率，因为它不再需要估算堆是否需要调整大小了。



所有用于调整代空间的命令行标志调整的都是新生代空间;新生代空间剩下的所有空间都被老年代占用。多个标志都能用于新生代空间的调整，它们分别如下所列。

-XX:NewRatio=N  设置新生代与老年代的空间占用比率。

-XX:NewSize=N  设置新生代空间的初始大小。

- XX:MaxNewSize=N 设置新生代空间的最大大小。

- XmnN 将NewSize和MaxNewSize设定为同一个值的快捷方法。


最初新生代空间大小是由NewRatio 指定大小，NewRatio 的默认值为2。影响堆空间大小的参数通常以比率的方式指定;这个值被用于一个计算空间分配的公式之中。下面是使用NewRatio计算空间的公式:

Initial Young Gen Size = Initial Heap Size / (1 + NewRatio)

代人堆的初始大小和NewRatio的值就能得到新生代的设置值。那么我们很容易得出，默认情况下，新生代空间的大小是初始堆大小的33% 


如果堆的大小扩张，新生代的大小也会随之增大，直到由MaxNewSize标志设定的最大容量。默认情况下，新生代的最大值也是由NewRatio的值设定的，不过它也同时受制于堆的最大容量](注意，不是初始大小)。

试图通过指定新生代的最大及最小值区间的方式调优新生代的结果是十分困难的。如果堆的大小是固定的(可以通过将-Xns 和-Xmx指定为相等的值实现)，通常推荐使用-Xmn标志将新生代也设定为固定大小。如果应用程序需要动态调整堆的大小，并希望有-一个更大(或者更小)的新生代，那就需要关注NewRatio值的设定,


###  快速小结

1.整个堆范围内，不同代的大小划分是由新生代所占用的空间控制的。

2.新生代的大小会随着整个堆大小的增大而增长，但这也是随着整个堆的空间比率波动变化的( 依据新生代的初始值和最大值)。
      
### 13

#### 久代/元空间的默认大小 
![默认堆的大小](https://github.com/gaoyuanyuan2/distributed/blob/master/img/70.png) 

这些内存区域的行为就像是分隔开的普通堆空间。它们会根据初始的大小动态地调整，需要的时候会增大到最大的堆空间。对于永久代而言，可以通过-XX:PermSize=N、-XX:MaxPermSize=N标志调整大小。而元空间的大小可以通过-XX:MetaspaceSize=N和-XX:MaxMetaspaceSize=N调整。

#### 元空间会过大吗?
     
由于元空间默认的大小是没有作限制的，因此Java8 (尤其是32位系统)的应用可能由于元空间被填满而耗尽内存。工具本地内存跟踪器(Native MemoryTracking, NMT)可以帮助诊断这种类型的问题。
如果元空间增长得过大，通过设置MaxMetaspaceSize你可以调整元空间的上限，将其限制为一个更小的值，不过这又会导致应用程序最后由于元空间耗尽，发生OutOfMemoryError异常。
解决这类问题的终极方来还是定位出为什么类的元空间会变得如此巨大。


堆转储的信息可以用于诊断存在哪些类加载器，而这些信息反过来可以帮助确定是否存在类加载器的泄漏，最终导致永久代(或者元空间)被耗尽。除此之外，
使用jmap和-permstat 参数(适用于Java 7)、或者-clstats参数(适用于Java 8)可以输出类加载器相关的信息。不过这些命令都不是非常稳定，所以不大推荐使用。



以16核CPU的机器同时运行4个JVM实例为例，每个JVM默认会启动13个垃圾收集线程。
如果四个JVM同时进行垃圾回收操作，机器上会启动大约52个CPU密集型线程竞争CPU资源。
这会导致大量的冲突，如果能够限制每个JVM最多启动4个垃圾收集线程，效率会高很多。
即使在同一个时刻，4个JVM中的线程不大可能同时进行GC操作，一个JVM上同时运行13个线程也意味着其他JVM上的应用程序线程不得不在一台总共有16个CPU，
且其中13个CPU被繁忙的垃圾收集任务100%占用的机器上竞争资源。这种情况下，将每个JVM的垃圾收集线程数限制到4个是一个比较合理的平衡。
注意，这个标志不会对CMS收集器或者G1收集器的后台线程数作设定( 虽然它们也会受设置的影响)


#### 快速小结

1.几乎所有的垃圾收集算法中基本的垃圾回收线程数都依据机器上的CPU数目计算得出，

2.多个JVM运行于同一台物理机上时，依据公式计算出的线程数可能过高，必须进行优化(减少)。



自适应调整在两个方面能提供重要的帮助。其一,这意味着小型应用程序不需要再为指定了过大的堆而担心。譬如用于调整应用服务器的命令行管理程序，这类型的程序通常使用16 MB (或者64 MB)的堆，即使默认的堆可能增长到
1GB那么大的容量。有了自适应调整之后，这种类型的应用程序不再需要额外花费精力去调优，平台默认的配置就能确保他们不会使用大量的内存。

其次，这意味着很多应用程序根本不需要担心它们堆的大小，如果需要使用的堆的大小超过了平台的默认值，他们可以放心地分配更大的堆，而不用关心其他的细节。JVM会自动.调整堆和代的大小，依据垃圾回收算法的性能目标，使用优化的内存量。自适应调整就是让自动调整生效的法宝。

不过，空间大小的调整终归栗花费一定的时间开销，这部分时间大多数消耗在GC停顿的时候。如果你投注了大量的时间精细地调优了垃圾回收的参数、定义了应用程序堆的大小限制，
可以考虑关闭自适应调整。如果应用程序的运行明显地可以划分成不同的阶段，你希望对这些阶段中的某--个阶段进行垃圾回收的优化，那么关闭自适应调优也是很有帮助的。

使用-XX: -UseAdaptiveSizePolicy标志可以在全局范围内关闭自适应调整功能(默认情况下，这个标志是开启的)。如果堆容量的最大、最小值设置成同样的值，与此同时新生代的初始值和最大值也设置为同样大小，自适应调整的功能会被关闭。
不过这时的Survivor空间是个例外，我们在下-章中会详细介绍其中的细节。

如果你想了解应用程序运行时JVM的空间是如何调整的，可以设置-xX:+PrintAdaptiveSizePolicy标志。开启该标志后，一旦发生垃圾回收，GC的日志中会包含垃圾回收时不同的代进行空间调整的细节信息。

#### 快速小结、

1.JVM在堆的内部如何调整新生代及老年代的百分比是由自适应调整机制。

2.通常情况下，我们应该开启自适应调整，因为垃圾回收算法依赖于调整后的代的大小来达到它停顿时间的性能目标。

3. 对于已经精细调优过的堆，关闭自适应调整能获得一定的性能提升。

### 14

多种方法都能开启GC的日志功能，其中包括:使用-verbose:gc 或-XX:+PrintGC 这两个标志中的任意一个能创建基本的GC日志(这两个日志标志实际上互为别名，默认情况下的GC日志功能是关闭的)
使用-XX:+PrintCCDetails 标志会创建更详细的GC日志。我们推荐使用-XX:+PrintGCDetails 标志(这个标志默认情况下也是关闭的) ;
通常情况下使用基本的GC且志很难诊断垃圾回收时发生的问题除了使里详细的GC日志，我们还推荐使用-XX:+PrintGCTimeStamps或者-XX:+PrintGCDateStamps, 便于我们更精确地判断几次GC操作之间的时间。
达两个参数之间的差别在于时间戳是相对于0 (依据JVM启动的时间)的值，而日期戳(date stamp)是实际的日期字符串。由于日期戳需要进行格式化，所以它的效率可能会受轻微的影响，不过这种操作并不频繁，它造成的影响也很难被我们感知。

默认情况下GC日志直接输出到标准输出，不过使用-xloggc:filename标志也能修改输出到某个文件。除非显式地使用-PrintCCDetails标志，否则使用-xloggc会自动地开启基本日志模式。
使用日志循环(Log rotation) 标志可以限制保存在GC日志中的数据量:对于需要长时间运行的服务器而言，这是一个非常有用的标志，否则累积几个月的数据很可能会耗尽服务器的磁盘。
通过-XX:+UseGCLogfileRotation -XX: NunberOfGCLogfiles=N-XX:GCLogfileSize=N标志可以控制日志文件的循环。默认情况下，
UseGCLogfileRotation标志是关闭的。开启UseGCLogfileRotation标志后，默认的文件数目是0 (意味着不作任何限制)，默认的日志文件大小是0 (同样也是不作任何限制)。
因此，为了让日志循环功能真正生效，我们必须为所有这些标志设定值。需要注意的是，如果设定的数值不足8 KB的话，日志文件的大小会以8 KB为单位规整。

根据需要，你可以手工地解析、研读垃圾回收日志，也可以利用一些工具来完成这部分工作。GC Histogram (http://java.net/projects/gchisto) 就是这些工具中的一员。GC 
Histogram能够读入GC日志，根据日志文件中的数据生成对应的图表和表格。

#### 快速小结

1.GC日志是分析GC相关问题的重要线索:我们应该开启GC日志标志(即使是在生产服务器上)。

2.使用PrintGCDetails标志能获得更详尽的GC日志信息。

3.使用工具能很有效地帮助我们解析和理解GC日志的内容，尤其是在对GC日志中的数据进行归纳汇总时，它们非常有帮助。4.使用jstat能动态地观察运行程序的垃圾回收操作。


新生代的空间使用在经历Full GC之后变为0字节(新生代的大小为339 MB)。 
老年代中的空间使用从457 MB减少到了392 MB，因此整个堆的使用从473 MB减少到了392 MB。
永久代空间的使用没有发生变化:在多数的Full GC中，永久代的对象都不会被回收。(如果永么代空间耗尽JVM 会发起Full GC回收永久代中的对象， 这时你会观察到永久代空间的变化-这是永久代进行回收唯
一的情况。这个例子使用的是Java 7;在Java 8中，类似的信息可以在元空间中找到)。由于Full GC要进行大量的工作，所以消耗了约1.3秒的Real时间，4.4 秒的CPU时间(同样源于使用了4个并行的线程)。

#### 快速小结

1. Throughput 收集器会进行两种操作，分别是Minor GC和Full GC。

2. 通过GC日志中的时间输出，我们可以迅速地判断出Throughput收集器的GC操作对应用程序总体性能的影响。

#### 快速小结
1.采用动态调整是进行堆调优极好的入手点。对很多的应用程序而言，采用动态调整就已经足够，动态调整的配置能够有效地减少JVM的内存使用。

2.静态地设置堆的大小也可能获得最优的性能。设置合理的性能目标，让JVM根据设置确定堆的大小是学习这种调优很好的人门课程。

### 14、CMS

使用可中断预清理阶段是由于标记阶段(严格说起来，它应该是最后的输出项)不是并发的，所有的应用线程进人标记阶段后都会被暂停。
如果新生代收集刚刚结束，紧接着就是一个标记阶段的话，应用线程会遭遇2次连续的停顿操作，CMS收集器希望避免这样的情况发生。
使用可中断预清理阶段的目的就是希望尽量缩短停顿的长度，避免连续的停顿。

新生代发生垃圾回收，同时老年代又没有足够的空间容纳晋升的对象时，CMS垃圾回收就会退化成Full GC。所有的应用线程都会被暂停，老年代中所有的无效对象都被回收，释放空间之后老年代的占用为1366 MB--这 
次操作导致应用程序线程停顿长达5.6秒。这个操作是单线程的，这就是为什么它耗时如此之长的原因之一(这也是为什么发生并发模式失效比堆的增长更加恶劣的原因之一)。

新生代发生垃圾回收，同时老年代又没有足够的空间容纳晋升的对象时，CMS垃圾回收就会退化成Full GC。所有的应用线程都会被暂停，老年代中所有的无效对象都被回收，释放空间之后老年代的占用为1366 MB--这 次操作导致应用程序线程停顿长达5.6秒。
这个操作是单线程的，这就是为什么它耗时如此之长的原因之一(这也是为什么发生并发模式失效比堆的增长更加恶劣的原因之一)。

因此，CMS收集器在新生代垃圾收集过程中(所有的应用线程都被暂停时)，对整个老年代空间进行了整理和压缩。
好消息是，随着堆的压缩，碎片化问题解决了(至少在短期内不是问题了)。不过随之而来的是长达28秒的冗长的停顿时间。
由于需要对整个堆进行整理，这个时间甚至比CMS收集器遭遇并发模式失效的时间还长的多，因为发生并发模式失效时，CMS收集器只需要回收堆内无用的对象。
这时的堆就像刚由Throughput收集器做完FullGC一样 :新生代空间完全空闲，老年代空间也已经整理过。


永久代空间用尽，需要回收时，就会发生这样的状况，应注意到，CMS收集后永久代空间大小减小了。
Java 8中，如果元空间需要调整，也会发生同样的情况。
默认情况下，CMS收集器不会对永久代(或元空间)进行收集，因此，它一旦被用尽，就需要进行FullGC,所有没有被引用的类都会被回收。
CMS高级调优一节会有针对性地介绍如何解决这种问题。

#### 快速小结

1.CMS垃圾回收有多个操作，但是期望的操作是MinorGC和并发回收(concurrent cycle)。

2.CMS收集过程中的并发模式失效以及晋升失败的代价都非常昂贵;我们应该尽量调优CMS收集器以避免发生这些情况。

3.默认情况下CMS收集器不会对永久代进行垃圾回收。


### 并发模式失效

初始时老年代空间中对象是一个接一个整齐有序排列的。当老年代空间的占用达到某个程度(默认值为70%)时，并发回收就开始了。一个CMS后台线程开始扫描老年代空间，寻找无用的垃圾对象时，竞争就开始了: CMS收集器必须在老年代剩余的空间
(30%) 用尽之前，完成老年代空间的扫描及回收工作。如果并发回收在这场速度的比赛中失利，CMS收集器就会发生并发模式失效。

有以下途径可以避免发生这种失效。

1.想办法增大老年代空间，要么只移动部分的新生代对象到老年代，要么增加更多的堆空间。

2.以更高的频率运行后台回收线程。

3.使用更多的后台回收线程。



自适应调优和CMS垃圾搜集

CMS收集器使用两个配置MaxGCPauseMllis=N和GCTineRatio=N来确定使用多大的堆和多大的代空间。

CMS收集与其他的垃圾收集方法一个显著的不同是除非发生Full GC，否则CMS的新生代大小不会作调整。由于CMS的目标是尽量避免Full GC,这意味着使用精细调优的CMS收集器的应用程序永远不会调整它的新生代大小。

程序启动时可能频发并发模式失效，因为CMS收集器需要调整堆和永久代(或者元空间)的大小。使用CMS收集器，初始时采用一个比较大的堆(以及更大的永久代/元空间)是一个很好的主意，这是一个特例，增大堆的大小反而帮助避免了那些失效。

#### 快速小结

1.避免发生并发模式失效是提升CMS收集器处理能力、获得高性能的关键。

2.避免并发模式失效(如果有可能的话)最简单的方法是增大堆的容量。

3.否则，我们能进行的下一个步骤就是通过调整CMSInitiatingOccupancy-Fraction参数，尽早启动并发后台线程的运行。
     
4.另外，调整后台线程的数目对解决这个问题也有帮助。

#### 快速小结

1.应用在CPU资源受限的机器上运行，同时又要求较小的停顿，这时使用增量式CMS收集器是一个不错的选择。

2.通过责任周期可以调整增量式CMS收集器:增加责任周期的运行时间可以避免CMS收集器发生并发模式失效。

### G1垃圾收集器

G1垃圾收集器是一种工作在堆内不同分区上的并发收集器。分区(region) 既可以归属于老年代，也可以归属于新生代(默认情况下，一个堆被划分成2048个分区)
，同一个代的分区不需要保持连续。为老年代设计分区的初衷是我们发现并发后台线程在回收老年代中没有引用的对象时，有的分区垃圾对象的数量很多，另一些分区的垃圾对象相对较少。
虽然分区的垃圾收集工作实际仍然会暂停应用程序线程，不过由于G1收集器专注于垃圾最多的分区，最终的效果是花费较少的时间就能回收这些分区的垃圾。
这种只专注于垃圾最多分区的方式就是G1垃圾收集器名称的由来，即首先收集垃圾最多的分区。

不过这一算法并不适用于新生代的分区:新生代进行垃圾回收时，整个新生代空间要么被回收，要么被晋升(对象被移动到Survivor空间，或者移动到老年代)。
新生代也采用分区机制的部分原因，是因为采用预定义的分区能够便于代的大小调整。

G1收集器的收集活动主要包括4种操作:

1.新生代垃圾收集;

2.后台收集，并发周期;

3.混合式垃圾收集;

4.以及必要时的Full GC。


#### 并发模式失效

G1垃圾收集启动标记周期，但老年代在周期完成之前就被填满，在这种情况下，G1收集器会放弃标记周期:

发生这种失败意味着堆的大小应该增加了，或者G1收集器的后台处理应该更早开始，或者是需要调整周期，让它运行得更快(譬如，增加后台处理的线程数)。

#### 晋升失败

G1收集器完成了标记阶段，开始启动混合式垃圾回收，清理老年代的分区，不过，老年代空间在垃圾回收释放出足够内存之前就会被耗尽。垃圾回收日志中，这种情况的现象通常是混合式GC之后紧接着一次FullGC。

#### 疏散失败

进行新生代垃圾收集时，Survivor空间和老年代中没有足够的空间容纳所有的幸存对象。这种情形在GC日志中通常被当成一种特别的新生代:

`60.238: [GC pause (young) (to-space overflow), 0. 41546900 secs]`

这条日志表明堆已经几乎完全用尽或者碎片化了。G1收集器会尝试修复这一失败，但是你可以预期，结果会更加恶化: G1收集器会转而使用Full GC。

解决这个问题最简单的方式是增加堆的大小，除此之外，其他一些可能的解决方案 高级调优。

#### 巨型对象分配失败

 使用G1收集器时，分配非常巨大对象的应用程序可能会遭遇另一种FullGC;参见“使用G1分配巨型对象”
 
目前为止没有工具可以很方便地专门诊断这种类型的失败，尤其是从标准垃圾收集8志中进行诊断。不过，如果发生了莫名其妙的 Full GC,其源头很可能是巨型对象分配导致的问题。

#### 快速小结

1.G1垃圾收集包括多个周期(以及并发周期内的阶段)。调优良好的JVM运行G1收集器时应该只经历新生代周期、混合式周期和并发GC周期。

2. G1的并发阶段会产生少量的停顿。

3.恰当的时候，我们需要对G1进行调优，才能避免Full GC周期发生。


#### G1垃圾收集器调优

G1垃圾收集器调优的主要目标是避免发生并发模式失败或者疏散失败，一旦发生这些失败就会导致Full GC。避免Full GC的技巧也适用于频繁发生的新生代垃圾收集，这些垃圾收集需要等待扫描根分区完成才能进行。

其次，调优可以使过程中的停顿时间最小化。下面所列的这些方法都能够避免发生FullGC。

1. 通过增加总的堆空间大小或者调整老年代、新生代之间的比例来增加老年代空间的大小。

2. 增加后台线程的数目(假设我们有足够的CPU资源运行这些线程)。

3. 以更高的频率进行G1的后台垃圾收集活动。

4. 在混合式垃圾 回收周期中完成更多的垃圾收集工作。



#### 1.调整G1垃圾收集的后台线程数

为了帮助G1赢得这场垃圾收集的比赛，可以尝试增加后台标记线程的数目(假设机器有足够的空闲CPU可以支撑这些线程的运行)。

调整G1垃圾收集线程的方法与调整CMS垃圾收集线程的方法类似:对于应用线程暂停运行的周期，可以使用ParallelCThreads标志设置运行的线程数;对于并发运行阶段可以

使用ConCCThreads标志设置运行线程数。不过，ConcGCThreads 标志的默认值在G1收集器中不同于CMS收集器。它的计算方法如下:

      ConcGCThreads = (ParallelGCThreads + 2) / 4^3

这个算法依然是基于整数的; G1收集器与CMS收集器的计算方法相差无几。


#### 2.调整G1垃圾收集器运行的频率

如果G1收集器更早地启动垃圾收集，也能贏得这场比赛。G1垃圾收集周期通常在堆的占用达到参数
-XX: InitiatingHeapOccupancyPercent=N设定的比率时启动，默认情况下该参数的值为45。

注意，跟CMS收集器不太--样，这个参数值的依据是整个堆的使用情况，不单是老年代的。

InitiatingHeapOccupancyPercent的值是个常数，G1收集器自身不会为了达到停顿时间目标而修改这个参数值。如果该参数设置得过高，应用程序会陷入Full 
GC的泥潭之中，因为并发阶段没有足够的时间在剩下的堆空间被填满之前完成垃圾收集。
如果该值设定得过小，应用程序又会以超过实际需要的节奏进行大量的后台处理。我们在介绍CMS收集器时讨论过，必须要有能支撑后台处理的CPU周期，因此消耗额外的CPU就不那么重要。

然而，这可能会带来非常严重的后果，因为并发阶段会出现越来越多的短暂应用线程的停顿。这些停顿会迅速累积起来，因此使用G1收集器时要避免频繁地进行后台清理。
并发周期结束之后，检查下堆的大小，确保InitiatingHeapOccupancyPercent的值大于此时堆的大小。

#### 3.调整G1收集器的混合式垃圾收集周期

并发周期之后、老年代的标记分区回收完成之前，G1收集器无法启动新的并发周期。因此，让G1收集器更早启动标记周期的另一个方法是在混合式垃圾回收周期中尽量处理更多的分区(如此一来最终的混合式GC周期就变少了)。

混合式垃圾收集要处理的工作量取决于三个因素。第一个因素是有 多少分区被发现大部分是垃圾对象。
目前没有标志能够直接调节这个因素:混合式垃圾收集中，如果分区的垃圾占用比达到35%，这个分区就被标记为可以进行垃圾回收。(这个因素在将来的某个时刻可能也能调整，在开源的实验版本中已经有名为-XX:G1MixedCLiveThresholdPercent=N的参数可以对其进行调整)。

第二个因素是G1垃圾收集回收分区时的最大混合式GC周期数，通过参数-XX:G1MixedCCountTarget=N可以进行调节。
这个参数的默认值为8;减少该参数值可以帮助解决晋升失败的问题(代价是混合武GC周期的停顿时间会更长)。


另一方面，如果混合式GC的停顿时间过长，可以增大这个参数的值，减少每次混合式GC周期的工作量。不过调整之前我们需要确保增大值之后不会对下一-次G1并发周期带来太大的延迟，否则可能会导致并发模式失败。

最后，第三个影响因素是GC停顿可忍受的最大时长(通过MaxGCPauseMillis参数设定)。MaxGCPauseMillis标志设定的混合式周期时长是向上规整的，如果实际停顿时间在停顿最大时长以内，G1收集器能够收集超过八分之一。

标记的老年代分区(或者其他设定的值)。增大MaxGCPauseMillis能在每次混合式GC中收集更多的老年代分区，而这反过来又能帮助G1收集器在更早的时候启动并发周期。

#### 快速小结

1.作为G1收集器调优的第一步，首先应该设定一一个合理的停顿时间作为目标。

2.如果使用这个设置后，还是频繁发生Full GC,并且堆的大小没有扩大的可能，这时就需要针对特定的失败采用特定的方法进行调优。

a.通过InitiatingHeapOccupancyPercent标志可以调整G1收集器，更频繁地启动后台垃圾收集线程。

b.如果有充足的CPU资源，可以考虑调整ConcGCThreads标志，增加垃圾收集线程数。

c.减小G1MixedGCCountTarget参数可以避免晋升失败。


#### 快速小结

1.设计Survivor空间的初衷是为了让对象(尤其是已经分配的对象)在新生代停留更多的GC周期。这个设计增大了对象晋升到老年代之前被回收释放的几率。

2.如果Survivor空间过小，对象会直接晋升到老年代，从而触发更多的老年代GC。

3.解决这个问题的最好方法是增大堆的大小(或者至少增大新生代)，让JVM来处理Survivor空间的回收。

4.有的情况下，我们需要避免对象晋升到老年代，调整晋升阈值或者Survivor空间的大小可以避免对象晋升到老年代。


#### 快速小结

对需要分配大量大型对象的应用，TLAB空间的调整就变得必不可少(不过，通常情况下，我们更推荐在应用程序中使用小型对象的做法)。

#### 快速小结

1. G1分区的大小是2的幂，最小值为1 MB。

2.如果堆的初始大小跟最大值相差很大，这种堆会有大量的G1分区，在这种情况下，应该增大G1分区的大小。;

3.如果要分配的对象大小超过了G1收集器分区容量的一半，对于这种应用程序，我们应该增大G1分区的容量，让G1分区能更好地适配这些对象。遵循这个原则，应用程序分配对象的大小至少应是512 KB (因为G1分区的最小值为1 MB)。

#### 快速小结

1. AggressiveHeap 是个历史悠久的调优标志，设计初衷是为了在强大的机器上运行单一JVM时调整堆的各种参数。

2.这个标志设定的值并没有随着JVM技术的发展同步调整，因此它的有效性从长远来看是值得质疑的(虽然到目前为止，这个标志还常常被使用)。

####  快速小结

 1.大多数的机器.上堆的初始空间和最大空间的默认值计算是比较直观的。2.达到堆大小的临界情况时，需要考虑的因素更多，计算也更加复杂。


#### 小结

如果垃圾收集花费的时间超出了你的预期，了解垃圾收集的内部工作原理能帮你决定采取哪些必要的步骤进行性能调优。

 现在我们已经了解了所有的细节，让我们回退一-步，决定选择什么方法，采用什么标志来调优垃圾收集器。
 
 
 下面是 一些问题集合，在调优之前，先试着回答这些问题，它们能帮你理清思路，选择合适的调优措施。


#### 你的应用能够忍受FullGC的停顿吗?

如果答案是肯定的，选择Throughput收集器能获得最高的性能，同时，使用的CPU和堆的大小也比其他的垃圾收集器少。
如果答案是否定的，你需要依据可用的堆大小做选择，如果可用的堆较小，你可以选择并发收集器，譬如CMS收集器或者G1收集器;
如果可用的堆比较大，推荐使用G1收集器。

#### 使用默认设置能达到你期望的性能目标吗?

尽量首先使用默认的设置。因为垃圾收集技术在不断发展成熟，自动调优大多数情况下取得的效果是最好的。
如果使用默认设置没有达到你需要的性能目标，请确认垃圾收集是否是性能瓶颈。
查看垃圾收集8志能帮我们了解JVM在垃圾收集上花费了多长时间、垃圾收集发生的频率是多少。
对于负荷较高的应用，如果JVM花在垃圾收集上的时间不超过3%，即使进行垃圾调优也不会得到太大的性能提升(不过，如果那些指标是你关注的方面，你仍然可以尝试通过调优缩短某些指标)。

#### 应用的停顿时间与你预期的目标接近吗?

如果停顿时间与你预期的目标很接近，调整最大停顿时间的设定可能是你需要做的。

如果不是，你需要进行其他的调整。如果停顿时间太长，但是应用的吞吐量正常，你可以尝试减小新生代的大小(如果瓶颈是Full GC的停顿，就减小老年代的大小) ;
调整之后，停顿的频率会增加，但是单次停顿的时长会变短。

#### 虽然GC的停顿时间已经非常短了，但应用的吞吐量依旧上不去?

这种情况下你需要增大堆的大小(至少要增大新生代)。但是，这并不意味着堆越大越好:更大的堆会导致更长的停顿时间。
即便是并发收集器，默认情况下，增大堆也还是意味着增大新生代，因此你会发现新生代的停顿时间变长了。
即便是这样，如果有可能，还是应该增大堆的大小，或者增大对应代的大小。

#### 你使用并发收集器吗?是否发生了由并发模式失败引起的Full GC ?

如果你有足够的CPU资源，可以尝试增加并发GC线程的数量，或者通过调整InitiatingHeapOccupancyPercent 
参数在更早的时候启动后台清理线程。对于G1收集器，如果有混合式垃圾收集尚未完成，并发周期就不会启动。
在这个时候，可以尝试降低混合式GC的回收目标(Mixed GC count target)。

#### 你使用并发收集器吗?是否发生了由晋升失敗引起的Full GC ?

在CMS收集器中，发生晋升失败意味着堆发生了碎片化。这种情况下，我们能做的事情不多:使用更大的堆，或者尽早地启动后台回收都能在一定程度上缓解堆的碎片化。

处理这种情况，更好的解决方法可能是使用G1收集器。G1 收集器中，疏散失败(To 空间溢出)表明遭遇了同样的情况，但是G1收集器能解决碎片化的问题，如果它的后台线程在更早的时候启动，且混合武GC的速度更快的话。

你可以尝试通过增大并发G1收集线程的数目，调整 InitiatingHeapOccupancyPercent,或者降低混合式GC的目标来解决G1收集器中堆碎片化的问题。

### 高级优化

#### 快速小结

1.有多种原因会导致抛出OutOfMemoryError,因此不要假设堆空间就是问题所在。

2.对于永久代和普通的堆，内存泄漏是出现OutOfMemoryError的最常见原因;堆分析工具可以帮助我们找到泄漏的根源。


#### 减少内存使用

在Java中，第一种更高效使用内存的方式是减少堆内存的使用。这句话不难理解:堆内存用的越少，堆被填满的几率就越低，需要的GC周期也越少。
而且有倍乘效应:新生代回收的次数更少，对象的晋升年龄也就不会很频繁地增加，这意味着对象被提升到老年代的可能性也降低了。
因此，FullGC 周期(或者是并发GC周期)也会减少。而且，如果这些Full GC周期能够清理更多内存，它们发生的频率也会降低。

#### 减少对象大小

对象会占用一定数量的堆内存，所以要减少内存使用，最简单的方式就是让对象小一些。
考虑运行程序的机器的内存限制，增加10%的堆有可能是无法做到的，但是堆中--半对象的大小减少20%，能够实现同样的目标。

### 快速小结

1.减小对象大小往往可以改进GC效率。

2.对象大小未必总能很明显地看出来:对象会被填充到8字节的边界，对象引用的大小在32位和64位JVM上也有所不同。

3.对象内部即使为null的实例变量也会占用空间。

### 快速小结

1.只有当常用的代码路径不会初始化某个变量时，才去考虑延迟初始化该变量。

2.一般不会在线程安全的代码上引入延迟初始化，否则会加重现有的同步成本。

3.对于使用了线程安全对象的代码，如果要采用延迟初始化，应该使用双重检查锁。


#### 快速小结

1.不可变对象为标准化(canonicalization) 这种特殊的生命周期管理提供了可能性。

2.通过标准化去掉不可变对象的冗余副本，可以极大减少应用消耗的堆内存。

#### JDK和Java EE中重用对象的一些例子，以及重用的原因:线程池线程初始化的成本很高。

*JDBC池*

数据库连接初始化的成本很高。EJB池EJB初始化的成本很高。

*大数组*
Java要求，一个数组在分配的时候，其中的每个元素都必须初始化为某个默认值(null、日或者false,根据具体情况而定)。对于很大的数组，这是非常耗时的。原生NIO缓冲区
不管缓冲区多大，分配一个直接的java.nio. Buffer ( 即调用allocateDirect()方法返回的缓冲区)，这个操作都非常昂贵。最好是创建-一个很大的缓冲区，然后通过按需切
割的方式来管理，以便将其重用于以后的操作。安全相关类MessageDigest、Signature 以及其他安全算法的实例，初始化的成本都很高。基于Apache的XML代码就是使用线程局部变量保存这些实例的。字符串编解码器对象
JDK中的很多类都会创建和重用这些对象。在大多数情况下，这些还是软引用，下一节将介绍。
StringBuilder协助者BigDecimal类在计算中间结果时会重用一个StringBuilder 对象。

*随机数生成器*
Random类和(特别是) SecureRandom 类，生成它们的实例的代价是很高的。

*从DNS查询到的名字*
网络查询代价很高。

*ZIP编解码器*
有一种有趣的变化，初始化的开销不是特别高，但是释放的成本很高，因为这些对象要依赖对象终结操作(finalization) 来确保释放掉所用的原生内存。更多细节，参见7.3.2节的“终结器和最终引用"。
此处讨论的对象池和线程局部变量两种方式，在性能上有些差别。下面详细看一下。


#### 快速小结

1.对象重用通常是一种通用操作，我们并不鼓励使用它。但是这种技术可能适合初始化成本高昂，而且数量比较少的一组对象。

2.在使用对象池还是使用线程局部变量这两种技术之间，应该有所取舍。一般而言，建设线程和可重用对象直接存在一一对应关系，则线程局部变量容易使用。


#### 快速小结

1.非确定引用(包括软引用、弱引用、虚引用和最终引用)会改变Java对象正常的生命周期，与池或线程局部变量相比，它可以以对GC更为友好的方式实现对象重用。

2.当应用对某个对象感兴趣，而且该对象在应用中的其他地方有强引用时，才应该使用弱引用。

3.软引用保存可能长期存在的对象，提供了一个简单的、对GC友好的LRU缓存。

4.非确定引用自身会消耗内存，而且会长时间抓住其他对象的内存;应该谨慎使用。

### 小结

内存管理对Java程序的快慢至关重要。调优GC非常重要，但是要获得最好的性能，在应用内必须有效地利用内存。

目前的硬件趋势往往不鼓励开发者考虑内存:如果我的笔记本有16 GB内存，我为什么要关心某个对象中有一个多余的、未使用的8字节大的对象引用呢?
我们还忘记了一点，编程中通常的时间和空间之间的取舍，有可能会变成时间和空间与时间(time/spaceand-time)之间的取舍:使用太多堆空间可能会降低性能，
因为需要更多GC。在Java中,管理堆仍然非常重要。

多数管理问题都围绕何时以及如何使用特殊的内存技术展开:对象池、线程局部变量和非确定引用。
明智地使用这些技术可以极大改进应用性能，但是过度使用也很容易引起性能下降。
在限量的情况下，也就是问题中的对象数目很少，而且有个边界时，使用这些内存技术会非常高效。

### 内存占用

在JVM使用的内存中，通常堆消耗的部分最多，但是JVM也会为内部操作分配一些内存。这类非堆内存就是原生内存。

应用中也可以分配原生内存(通过JNI调用malloc()和类似方法，或者是使用New IO,即NIO时)。JVM使用的原生内存和堆内存的总量，就是一个应用总的内存占用(Footprint)。

线程栈是个例外。JVM每次创建线程时，操作系统会分配一些原生内存来保存线程栈，向进程提交更多内存(至少要等到线程退出)。线程栈是在创建时全部分配的。


8.1.2  内存占用最小化

为将JVM的内存占用最小化，应该限制以下几个部分的内存使用量。


*堆*

堆是最大的一块内存，尽管有些出人意料，它可能只占总内存占用的50%到60%。
可以将堆的最大值设置为一个较小的值(或者设置GC调优参数，比如控制堆不会被完全占满)，以此限制程序的内存占用。

*线程栈*

线程栈非常大

*代码缓存*

代码缓存使用原生内存来保存编译后的代码。第4章讨论过，代码缓存也可以调优(不过，如果因为空间的限制而导致所有代码无法编译，对性能也会有不利影响)。

*直接字节缓冲区*










     



### 7、Tomcat

* servlet jsp rest webSocket 

#### Tomcat架构

1.  目录结构:介绍Tomcat目录结构,如`config`、`webapps` 等目录的用途,和各种配置文件的使用场景,如`server.xml`  `context.xm1` 以及`catalina.policy` 等

2.  应用上下文:理解Web应用上下文与Servlet之间的关系,掌握上下文路径以及其他相关属性的使用场景连接器:了解阻塞式I/O以及非阻塞式I/O的配置方式,同时掌握请求连接的设置方法

3.  线程池:了解Tomcat线程池的配置方式以及理解它与Java标准实现的关系

4.  Java EE组件: JMX、 JDNI架构以及使用方法。

#### 嵌入式Tomcat

1.  Maven插件:利用Maven Tomcat插件构建嵌入式Tomcat容器

2.  API编程:通过Tomcat API的角度,深入理解Tomcat运行机制以及组件之间的关系。

3.  Spring Boot :深入探究Spring Boot利用嵌入式Tomcat API如何实现自定义容器

#### 架构图

![整体架构](https://github.com/gaoyuanyuan2/distributed/blob/master/img/30.png) 

#### 目录结构

1.  conf目录

`catalina.policy` : Tomcat安全策略文件,控制JVM相关权限,具体可以参考`java.security.Permission`

`catalina.properties`: Tomcat Catalina行为控制配置文件,比如Common Classloader

`logging.properties`: Tomcat日志配置文件, JDK Logging

`server.xml`: Tomcat Server配置文件

`GlobalNamingResource`:  全局JNDI资源

`context.xml`:  全局Context配置文件

`tomcat-users.xml` : Tomcat角色配置文件, = ( Realm文件实现方式)

`web.xml`  : Servlet标准的web.xml部署文件, Tomcat默认实现部分配置入内:

`org.apache.catalina.servlets.DefaultServlet`

`org.apache.jasper.servlet.JspServlet`

2.  lib目录

*  Tomcat存放公用类库

* `ecj-*.jar` : Eclipse Java编译器

* `jasper.jar` : JSP编译器

3.  logs目录

* `localhost.${date}.log` :当Tomcat应用起不来的时候,多看该文件,比如:类冲突

* `NoClassDefFoundError`

* `ClassNotFoundException`
     
* `catalina.${date}.log`: 控制台输出，`System.out` 外置

4.  webApps 

5.  work

#### 部署

1.  放置在webapps目录

直接拖过去

2.  修改 `conf/server.xml`

添加Context元素:

```xml
<Context docBase=" ${webAppAbsolutePath}" path="/" reloadable="true" />
<Context docBase=" ${webAppAbsolutePath}" path="/tomcat" reloadable="true" />
```

熟悉配置元素可以参考`org.apache.catalina.core.StandardContext`  setter方法

`Container`

`Context`

该方式不支持动态部署,建议考虑在生产环境使用。

3.  独立 `context` xml配置文件

* 首先注意 `conf\Catalina\localhost`

* 独立context XML配置文件路径: `${TOMCAT_ HOME}/conf/Catalina/localhost+${ContextPath}.xml`

* 注意:该方式可以实现热部署、热加载，因此建议在开发环境使用。

热部署：`reloadable="true" `

```xml
<Context docBase="E:/Downloads/tomcat/target/tomcat-1.0-SNAPSHOT" reloadable="true" />
```

4.  改appBase
```xml
<Host name="localhost" unpackWARs="true" appBase="webapps" autoDeploy="true ">
```

#### 连接器

参考文件: https://tomcat.apache.org/tomcat-7.0-doc/config/http.html

实现类:  `org.apache.catalina.connector.Connector`

![Connector Comparison](https://github.com/gaoyuanyuan2/distributed/blob/master/img/31.png) 

#### 编码

默认 ISO-8859-1

解决编码问题

```xml
<Connector port="8080"  protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" URIEncoding="UTF-8"/>
```

```java
req.setCharacterEncoding("UTF-8");
resp.setCharacterEncoding("UTF-8");
resp.setContentType("text/html ;charset=UTF-8");
```

#### 问答互动

1.  如果配置path的话是以文件名为主还是以配置的为主
独立context XML配置文件时,设置`path` 属性是无效的。

2.  根独立context XML配置文件路径

`${TOMCAT_ HOME }/conf/${Engine.name }/${HOST.name }/ROOT.xm1`

3.  如果实现热部署

调整`<context>` 元素中的属性`reloadable="true"`   

4.  连接器里面的线程池是用的哪个线程池

    注意`conf/server.xm1l`文件中的一段注释:
    
 ```xml
 <Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1" connectionTimeout=" 20000" 
 redirectPort="8443"/>
```
```java

import  org.apache.catalina.Executor;

public interface Executor extends java.util.concurrent.Executor, Lifecycle{

      public String getName();

      void execute( Runnable command, long timeout, TimeUnit unit);
}
```

标准实现: `org.apache.catalina.org.StandardThreadExecutor`将连接处理交付给Java标准线程池:

`org.apache.tomcat.util.threads.ThreadPoolExecutor`

后面版本用队列，生产者，消费者。

5.  INDI能不能稍微说下之前只是在数据源的时候用过,但是不是太理解

```xml
<Context ...
    <Resource name= "mail/Session" auth="Container" type="javax.mail.Session" mail.smtp .host="localhost"/>
</Context>
```
```java
    Context  initCtx =  new InitialContext();
    
    Context envCtx =  (Context)initCtx.lookup("java:comp/env");
    Session  session = (Session) envCtx.lookup( mail/Session");
    
    Message message = new MimeMessage(session) ;
    
    message.setFrom( newInternetAddress ( request. getParameter("from" )));
    InternetAddress to[] = new InternetAddress [1];
    
    to[0] = new InternetAddress(request.getParameter("to"));
    message.setRecipients(Message RecipientType.To, to);
    message.setSubject(request.getParameter("subject"));

```


#### Web技术栈

#### Servlet技术栈

* servlet 容器启动spring

#### Web Flux ( Netty )

#### BIO NIO

* NIQ并非一定能够提高性能,比如请求数据量较大, NIO性能比BIQ还要差

* NIQ多工，读、写,同步的

* 数量多效率高，数据量高反而效率低

#### Tomcat Maven插件

#### Tomcat API接口

#### Spring Boot嵌入式Tomcat 

#### Web自动动装

1.  API角度分析

* Servlet3.0 + API实现  `ServletContainerInitializer`

2.  容器角度分析

* 传统的Web应用,将webapp部署到Servlet容器中。

* 嵌入式Web应用,灵活部署,任意指定位置(或者通过复杂的条件判断)

* Tomcat 7是Servlet 3.0的实现，`ServletContainerInitializer`

* Tomcat 8是Servlet 3.1的实现， NIO `HttpServletRequest` 、`HttpServletResponse`

#### jar启动

* `java -jar` 或者`jar` 读取`.jar`

* 参考JDK API : `java.util.jar.Manifest`

META-INF /MANIFEST.MF , 其中属性Main-Class 就是引导类所在。

####  Tomcat Maven插件

1.  Tomcat 7 Maven插件

```xml
<groupId>org.apache.tomcat.maven</groupId>
<artifactId>tomcat7-maven-plugin</artifactId>
<version>2.1</version>
```

```properties
Manifest-Version: 1.0
Main-Class: org.apache.tomcat.maven.runner.Tomcat7RunnerCli
```

得出Tomcat 7可执行jar引导类是

`org.apache.tomcat.maven.runner.Tomcat7RunnerCli`

#### Tomcat API接口

#### Embedded

#### TomcatService

#### Engine

#### Host

#### ConnectorContext

* 1.  创建Tomcat实例

* `org.apache.catalina.startup.Tomcat`

* Maven坐标: `org. apache. tomcat . embed : tomcat-embed-core:7.0.37`

2.  设置Host对象

```java
Host  host  =  tomcat.getHost() ;
host.setName("localhost");
host.setAppBase("webapps");|
```

3.  设置Classpath

* Classpath读取资源:配置、类文件

* conf/web. xm1作为配置文件,并且放置Classpath目录下(绝对路径)

4.  设置DemoServlet

```java
//添加DemoServlet到Tomcat容器
Wrapper wrapper = tomcat.addServlet(contextPath, "DemoServlet", newDemoServlet());
wrapper.addMapping("/demo");
```

#### Spring Boot 嵌入式 Tomcat

#### EmbeddedServletContainerCustomizer

#### ConfigurableEmbeddedServletContainer

#### EmbeddedServletContainer

#### TomcatContextCustomizerTomcatConnectorCustomizer

#### Tomcat配置调优

* 1.  减少配置优化

场景一:假设当前应REST应用(微服务)

分析:它不需要静态资源, Tomcat容器静态和动态

* 静态处理:`DefaultServlet`

* 优化方案:通过移除`conf/web.xm1`中`org.apache.catalina.servlets.DefaultServlet`

* 动态: `JspServlet`

* 优化方案:通过移除`conf/web.xm1`中`org.apache.jasper.servlet.JspServlet`

* `DispatcherServlet` : Spring Web MVC应用Servlet 

* `jspServlet` :编译并且执行Jsp页面

* `DefaultServlet` : Tomcat处理静态资源的Servlet

* Last-Modified:如果服务器端的资源没有变化，则自动返回 HTTP 304 （Not Changed.）状态码，内容为空，这样就节省了传输数据量

2.  移除welcome-file-list

```xml
<welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file> index.htm</welcome-file>
    <welcome-file> index.jsp</welcome-file>
</welcome-file-list>
```

3.  移除Session设置

* 如果程序是REST JSON Content-Type或者MIME Type : application/json。移除Session设置

* 对于微服务/REST应用,不需要Session ,因为不需要状态。Spring Security QAuth 2.0、JWT

* Session通过jsessionld进行用户跟踪, HTTP无状态,需要一一个ID与当前用户会话联系。Spring Session HttpSession jessionld作为Redis ,实现多个机器登录,用户会话不丢失。

* 存储方法: Cookie、URL 重写、  SSL。

4.  移除Valve 

* Valve 类似于Filter

* 移除AccessLogValve, 可以通过Nginx的Access Log替代，Valve |实现都需要消耗Java应用的计算时间。

场景二:需要JSP的情况

分析: JspServlet无法，了解JspServlet处理原理

Servlet周期:

* 实例化: Servlet和Filter实现类必须包含默认构造器。反射的方式进行实例化。

* 初始化: Servlet容器调用Servlet或Filter init()方法

* 销毁: Servlet容器关闭时, Servlet或者Filter destroy()方法被调用

* Servlet或者Filter 在一个容器中 ,是一般情况在一个Web App中是一个单例 ,不排除应用定义多个。

* JspServlet相关的优化ServletConfig 

参数:

需要编译

* compiler
* modificationTestInterval

不需要编译

* development 设置false
* development = false , 那么,这些JSP要如何编译。

优化方法:

* Ant Task执行JSP编译

* Maven插件: `org.codehaus.mojo:jspc-maven-plugin`

```xml
<dependency>
    <groupId>org.apache.sling</ groupId>
    <artifactId>jspc-maven-plugin</artifactId>
    <version>2.1.0</version>
</dependency>

```

JSP ->翻译.jsp或者.jspx文件成.java ->编译.class

总结, `conf/web.xml`  作为Servlet应用的默认`web.xml` ,实际上,应用程序存在两份`web.xm1` ,其中包括应用的`web.xml`,最终将两者合并。
JspServlet如果development参数为true ,它会自定检查文件是否修改,如果修改重新翻译,再编译(加载和执行)。言外之意, JspServlet开发模式可能会导致内存溢出。卸载Class不及时所知道Perm区域不够。

JspServlet如果development参数为true ,它会自定检查文件是否修改,如果修改重新翻译,再编译(加载和执行)。言外之意, JspServlet开发模式可能会导致内存溢出。卸载Class不及时会导致Perm区域不够。

* ParentClassLoader -> 1.class 2.class 3.class

* ChildClassLoader -> 4.class , 5.class

* ChildClassloaderload    1-5.class

* 1.class需要卸载,需要将ParentClassLoader设置为null ,当Classloader被GC后, 1-3 class全部会被卸载。

* 1.class它是文件,文件被JVM加载, 二进制> Verify->解析

#### 修改连接池数量

* 通过server.xml

```xml
 <Connector executor="tomcatThreadPool"
               port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
                       maxThreads="150" minSpareThreads="4"/>
```

* 通过程序来理解，`Executor`实际的Tomcat 接口:

* `org.apache.catalina.Executor`

* 扩展:J.U.C标准接口`java.util.concurrent.Executor`

* 实现: `org.apache.catalina.core.StandardThreadExecutor`

* 线程数量

```java
     /**
     * max number of threads
     */
    protected int maxThreads = 200;

    /**
     * min number of threads
     */
    protected int minSpareThreads = 25;
    
    public void setMaxThreads(int maxThreads) {
        this.maxThreads = maxThreads;
        if (executor != null) {
            executor.setMaximumPoolSize(maxThreads);
        }
    }
    
    public void setMinSpareThreads(int minSpareThreads) {
        this.minSpareThreads = minSpareThreads;
        if (executor != null) {
            executor.setCorePoolSize(minSpareThreads);
        }
    }
```
* 线程池:

* `org.apache.tomcat.util.threads.ThreadPoolExecutor` (`java.util.concurrent.ThreadPoolExecutor`)

总结: Tomcat 10连接器使用的线程池实际标准的Java线程池的扩展,最大线程数量和最小线程数量实际上分别是MaximumPoolSize和CorePoolSize. 

#### 通过JMX

* 观察StandardThreadExecutor是否存在调整线程池数量的API

评估一些参考:

1.正确率

2. Load ( CPU-> JVM GC )

3. TPS/QPS(越大越好)

4. CPU密集型(加密/解密、算法)

5. 1/O密集型,网络、文件读写等


#### 问题:到底设置多少的线程数量才是最优?

首先,评估整体的情况量,假设100W QPS ,有机器数量100台,每台支撑1w QPS。

第二,进行压力测试，需要一些测试样本, JMeter来实现,假设一次请求需要RT 10ms，1秒可以同时完成100个请求。10000/ 100= 100线程。

* 确保, Load太高。减少Full GC, GC取决于JVM堆的大小。  执行一次操作需要5MB内存，50GB。

* 20 GB内存,必然执行GC。要不调优程序,最好对象存储外化,比如Redis ,同时又需要评估Redis网络开销。又要评估网卡的接受能力。

第三,常规性压测,由于业务变更,会导致底层性能变化。


默认算法
```jshelllanguage
java -jar -server -XX:-PrintGCDetails -Xloggc: ./1g/gc.1og -XX: +HeapDumpOnOutOfMemoryError 
-Xms1g -Xmx1g -XX:MaxGCPauseMillis=250 -Djava.awt.headless=true stress-test-demo-0.0.1-SNAPSHOT.jar
```

G1算法
```jshelllanguage
java -jar -server -XX:-PrintGCDetails -Xloggc: ./1g/g1-gc.1og -
XX:+He apDumpopoutOfMemoryError -Xms1g -Xmx1g -XX:+UseG1GC -XX: +UseNUMA -
XX:MaxGCPauseMillis=250 -Djava.awt.headless=true stress-test-demo-0.0.1-SNAPSHOT.jar
```

*-server 主要提高吞吐量,在有限的资源,实现最大化利用
*-client 主要提高响应时间,主要是提高用户

SpringBoot

`application. properties`

```properties

#<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"maxThreads="9" minSpareThreads="9"/>

#线程池大小
server.tomcat.maxThreads = 99

server.tomcat.minSpareThreads = 9

#取消Tomcat AccessLogValve

server.tomcat.accesslog.enabled = false

#取消JspServlet
server.jspServlet.registered=false
```

#### 程序调优
#### JVM参数调优

### 8、性能优化MySQL
#### 1. 架构图
![架构图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/32.png) 

![架构图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/33.png) 

![架构图](https://github.com/gaoyuanyuan2/distributed/blob/master/img/34.png) 

##### 引擎介绍 

|比较类别 |Innodb |Myisam|
|:--:|:--:|:--:|
|存储文件| .frm 表定义文件 .ibd 数据文件|    .frm表定义文件 .myd数据文件  .myi索引文件|
|锁 |表锁、行锁|  表锁|
|事务 |ACID| 不支持|
|CRDU |读、写|  读多|
|count| 扫表| 专门存储的地方|
|索引结构| B+ Tree |B+ Tree|

#### 2. 性能
1. 影响性能的因素
1) 人为因素-需求
*count(*) 论坛实时、 准实时、有误差
2) 程序员因素一面向对象
3) Cache
4) 对可扩展过度追求
5) 表范式
6) 应用场景
OLTP On-Line Transaction Processioning

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/35.png) 

* IO per second

OLAP  On-Line Analysis Processing

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/36.png) 

2. 提高性能
1) 索引
*索引结构

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/37.png) 

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/38.png) 

* B+ Tree 自增id做索引效率更高
索引有序，排序效率高
查看缓存 
```sql
show variables like '%sort%'
```
group by 基于排序（选排序再分组）
distinct 基于group by

3. 锁
*行锁

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/39.png) 

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/40.png) 

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/41.png) 

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/42.png) 

无索引自动升级为表锁
死锁
```sql
-- 释放锁
unlock tables 
-- 写锁
lock table user write
-- 间隙锁(范围锁)
update user set name = 'xxx' where id > 1 and id < 4 
```

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/46.png) 

4. 优化


select * from table_name [ force index( idx_name ) ]  where condition


![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/43.png) 

1)QEP Query Execution Plan

使用 加上explain

#### 我们先看一下在MySQL Explain功能中给我们展示的各种信息的解释

1. ID: Query 0ptimizer所选定的执行计划中查询的序列号;

2. Select_type: 所使用的查询类型，主要有以下这几种查询类型

DEPENDENT SUBQUERY: 子查询中内层的第一个 SELECT，依赖于外部查询的结果集;

DEPENDENT UNION:子查询中的UNION，且为UNION中从第二个SELECT开始的后面所有

SELECT，同样依赖于外部查询的结果集;

PRIMARY:子查询中的最外层查询，注意并不是主键查询;SIMPLE:除子查询或者UNION之外的其他查询;

SUBQUERY:子查询内层查询的第一个SELECT，结果不依赖于外部查询结果集;UNCACHEABLE SUBQUERY:结果集无法缓存的子查询;

UNION: UNION 语句中第二个SELECT开始的后面所有SELECT，第一个SELECT为PRIMARYUNION RESULT: UNION 中的合并结果;

3. Table: 显示这一步所访问的数据库中的表的名称:

4. Type:告诉我们对表所使用的访问方式，主要包含如下集中类型:

* all: 全表扫描

* const:读常量，且最多只会有一条记录匹配，由于是常量，所以实际上只需要读一次;

* eq_ ref: 最多只会有一条匹配结果，一般是通过主键或者唯一  键索引来访问;O fulltext:

* index:全索引扫描;

* index_merge:查询中同时使用两个(或更多)索引，然后对索引结果进行merge之后再读取表数据:

* index_subquery:子查询中的返回结果字段组合是一个索引(或索引组合)，但不是一个主键或者唯一索引:

* rang:索引范围扫描;

* ref: Join 语句中被驱动表索引引用查询:

* ref_or_null:与ref的唯-区别就是在使用索引引用查询之外再增加一个空值的查询

* system:系统表，表中只有一行数据:

* unique_subquery: 子查询中的返回结果字段组合是主键或者唯-约東;

依次从好到差: system , const , eq_ ref , ref , fulltext, ref_ or_ null ,

unique_subquery ，index_ subquery , range , index_ merge , index , ALL

![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/44.png) 

5. Possible_ keys:该查询可以利用的索引.如果没有任何索引可以使用，就会显示成null,这一项内容对于优化时候索引的调整非常重要;

6.  Key: MySQL Query Optimizer 从possible. keys 中所选择使用的索引;Key_ len:被选中使用索引的索引键长度;

7.  Ref:列出是通过常量(const) ，还是某个表的某个字段(如果是join)来过滤(通过key)的;

8.  Rows: MySQL Query Optimizer通过系统收集到的统计信息估算出来的结果集记录条数;

9.  Extra:  查询中每一一步实现的额外细节信息，主要可能会是以下内容: 

* Distinct:查找distinct值，所以当mysql找到了第一条匹配的结果后，将停止该值的查询而转为后面其他值的查询;

* Full scan on NULL key: 子查询中的一种优化方式， 主要在遇到无法通过索引访问null值的使用使用;

* Impossible WHERE noticed after reading const tables: MySQL Query Optimizer通过收集到的统计信息判断出不可能存在结果;

* No tables: Query 语句中使用FROM DUAL 或者不包含任何FROM 子句;

* Not exists: 在某些左连接中MySQL Query Optimizer所通过改变原有Query 的组成而使用的优化方法，可以部分减少数据访问次数;

* Range checked for each record (index map: N):通过MySQL 官方手册的描述，当MySQL Query Optimizer没有发现好的可以使用的索引的时候，如果发现如果来自前面的表的列值已知，可能部分索引可以使用。对前面的表的每个行组合，MySQL 检查是否可以使

* 用range或index_ merge访问方法来索取行。

* Select tables optimized away:当我们使用某些聚合函数来访问存在索引的某个字段的时候，MySQL Query 
Optimizer会通过索引而直接一次定位到所需的数据行完成整个查询。当然，前提是在Query中不能有GROUP BY操作。如使用MIN()或者MAX ()的时候;

* Using file sort: 当我们的Query 中包含ORDER BY操作，而且无法利用索引完成排序操作的时候，MySQL Query 0ptimizer不得不选择相应的排序算法来实现。

* Using index:所需要的数据只需要在Index即可全部获得而不需要再到表中取数据;

* Using index for group-by: 数据访问和Using index 一样，所需数据只需要读取索引即可，而当Query 中使用了GROUP BY或者DISTINCT 
子句的时候，如果分组字段也在索引中，Extra 中的信息就会是Using index for group-by;

* Using temporary:  当MySQL 在某些操作中必须使用临时表的时候，在Extra信息中就会出现Using temporary 。主要常见于GROUP BY和ORDER BY等操作中。

* Using where:如果我们不是读取表的所有数据，或者不是仅仅通过索引就可以获取所有需要的数据，则会出现Using where 信息;

* Using where with pushed condition:  这是一个仅仅在NDBCluster 存储引擎中才会出现的信息，而且还需要通过打开Condition Push 

down优化功能才可能会被使用。控制参数为encine condition nuishdown 。

5.  Profiling (有个概念)
```sql
set profiling=1;

select nick_ name ,count(*) from user group by nick_name;show profiles;

show profile cpu,block io for query 75;

```
1. join \ order by \group by解释


![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/45.png) 



