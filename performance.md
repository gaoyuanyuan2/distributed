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



