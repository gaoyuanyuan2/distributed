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
<br><br>
![JVM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/27.png) 

<br><br>
![JMM](https://github.com/gaoyuanyuan2/distributed/blob/master/img/28.png) 
<br><br>
<br>1.什么样的对象需要被GC
<br><br>1.  引用计数法
<br><br>2.  可达性分析GCRoots （解决相互引用无法回收的问题）
    <br>虚拟机栈中本地变量表引用的对象方法区中
    <br>类静态变量引用的对象常量引用的对象
    <br>本地方法栈中JNI引用的对象
<br><br>3.  不可达是不是就一定会被回收
    <br>finalize()

