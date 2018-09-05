## 性能优化
#### QPS: 每秒钟处理完请求的次数
#### TPS：每秒钟处理完的事务次数
### 1、CPU
<br>1.  top  查看进程
<br>按1 可以看cpu信息
<br>按shift + h 看线程
<br><br>2.  top -H -p  17343 检查进程内运行的线程
<br><br>3.  cat /proc/cpuinfo 查看cpu
<br><br>
http://man7.org/linux/man-pages/man1/top.1.html

<br><br>查找线程一直占用cpu的问题
<br>curl localhost:8080/hello
<br>cpu 都被占了，很卡，但是能用。因为会切换。
<br>jstack 38983 > a.txt （线程tack）
<br>printf "%x \n " 12455线程号转16进制  查询线程号即可
<br>grep -A 5 12af a.txt

<br><br>4.  vmstat
<br>vmstat 1 每秒看cpu
<br><br>http://www.man7.org/linux/man-pages/man8/vmstat.8.html

### 2、Memory
<br>free -g 看内存g
<br>free -m 看内存m
<br>df -h james/ 查看文件大小， -h 人能看懂的
<br>df -h 查看整个文件系统占有率
<br><br>http://www.man7.org/linux/man-pages/man1/free.1.html

### 3、IO
<br>iostat 1
<br>iostat -dx 1

### 4、NetWork
<br>nicstat 查看网卡

### 5、测试
<br>1.  秒杀 js过滤99%、Nginx过滤99% 
<br><br>2.  业务梳理比技术好
<br><br>3.  %99 都是数据库问题
<br><br>3.  AB局部更新，防压力
<br><br>4.  push
<br><br>5.  能沟通解决问题就不用用技术

### 6、JVM
<br>1.  软件层面机器码的翻译
<br><br>2.  内存管理
<br><br>3.  永久代溢出，Meta Space 自动扩容
<br><br>4.  对象生命周期不一样所以分代
<br><br>5.  分配担保

#### 栈
<br>1.栈描述的是方法执行的内存模型。每个方法被调用都会创建一一个栈帧(存储局部变量、操作数、方法出口等)
<br><br>2.JVM为每个线程创建一个栈 ,用于存放该线程执行方法的信息(实际参数、局部变量等)
<br><br>3.栈属于线程私有，不能实现线程间的共享!
<br><br>4.栈的存储特性是“先进后出,后进先出”
<br><br>5.栈是由系统自动分配,速度快!栈是一个连续的内存空间! 
#### 堆
<br>堆的特点如下: 
<br><br>1.堆用于存储创建好的对象和数组(数组也是对象)
<br><br>2. JVM只有一个堆，被所有线程共享
<br><br>3.堆是一个不连续的内存空间,分配灵活,速度慢!
<br><br>GC管理的主要区域，基本采用分代收集算法。
#### 方法区
<br>方法区(又叫静态区)特点如下:
<br><br>1.JVM只有一个方法区，被所有线程共享!
<br><br>2.方法区实际也是堆，只是用于存储类、常量相关的信息!
<br><br>3.用来存放程序中永远是不变或唯一的内容。 (类信息[Class对象]、静态变量、字符串常量等)
#### 执行引擎
<br>最核心的组件，它负责执行虚拟机的字节码。一般会先编译成机器码后执行。
#### 直接内存
<br>并不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范定义的内存区域,
<br>但这部分内存也被频繁的使用。NIO引入一种基于通道（Channel）与缓冲区（Buffer）的I/O方式，
<br>它可以使用Native函数库直接分配堆外内存，提高性能,避免了在Java堆和Native堆中来回复制数据。
#### 程序计数器
<br>是一块较小的内存空间，它可以看作是当前线程所执行的字节码的行号指示器。在虚拟机的概念模型里，
字节码解释器工作时候是通过改变这个计数器的值来选取下一条需要执行的字节码指令、分支、循环、跳转、异常处理、
线程恢复等基础功能都需要依赖这个计数器来完成。
<br><br>
![JVM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/27.png) 

<br><br>
![JMM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/28.png) 
<br><br>
#### 什么样的对象需要被GC
<br><br>1.  引用计数法
<br><br>2.  可达性分析GCRoots （解决相互引用无法回收的问题）
    <br>虚拟机栈中本地变量表引用的对象方法区中
    <br>类静态变量引用的对象常量引用的对象
    <br>本地方法栈中JNI引用的对象
<br><br>3.  不可达是不是就一定会被回收
    <br>finalize()

