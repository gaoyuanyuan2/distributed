# Docker



## Docker 是什么

1. Docker是一个开源的应用容器引擎,基于Go语言并遵从Apache2.0协议开源。
Docker可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中,然后发布到任何流行的Linux机器上,也可以实现虚拟化。

容器是完全使用沙箱机制,相互之间不会有任何接口，更重要的是容器性能开销极低。

Docker支持将软件编译成一个镜像，然后在镜像中各种软件做好配置,将镜像发布出去,其他使用者可以直接使用这个镜像。运行中的这个镜像称为容器，容器启动是
非常快速的。

2. Docker核心概念

1) docker镜像(images) : Docker镜像是用于创建Docker容器的  Clients  Hosts  Registries模板。

2) docker容器(Container) :容器是独立运行的一个或一组应用。 

3) 客户端(Client) :客户端通过命令行或者其他工具使用 Docker  APl与Docker的守护进程通信。

4) docker主机(Host) :一个物理或者虚拟的机器用于执行Docker守护进程和容器。

5) docker仓库(Registry) : Docker仓库用来保存镜像,可以理解为代码控制中的代码仓库。

6) DockerHub(tts://hub.docker.com)提供了庞大的镜像集合供使用。

## 对比

||Docker容器| 虚拟机(VM)|
|:--:|:--:|:--:|
|操作系统|与宿主机共享OS|宿主机OS,上运行虚拟机OS|
|存储大小|镜像小，便于存储与传输|镜像庞大(vmdk、vdi等)|
|运行性能|几乎无额外性能损失|操作系统额外的CPU、内存消耗|
|移植性|轻便、灵活，适应于Linux|笨重， 与虚拟化技术耦合度高|
|硬件亲和性|面向软件开发者|面向硬件运维者|
|部署速度|快速，秒级|较慢，10s以上|

镜像 容器 -> 类 对象

## 解决问题

* 我本地运行没问题啊! 

* 系统好卡,哪个哥们又写死循环了? !

* 双11来了,服务器撑不住啦!

## 优点:

资源利用率高

高效性

易于扩展、伸缩

 Go to Product快速

一致性

可封装性

隔离性



### 一次构建、随处运行

更快速的应用交付和部署

更便捷的升级和扩缩容

更简单的系统运维

更高效的计算资源利用

### Why Docker

更轻量:基于容器的虚拟化，仅包含业务运行所需的runtime环境，CentOS/Ubuntu基础镜像仅170M;宿主机可部署100~ 1000个容器

更高效:无操作系统虚拟化开销

* 计算:轻量，无额外开销

* 存储:系统盘aufs/dm/overlayts;数据盘volume

* 网络:宿主机网络，NS隔高

更敏捷、更灵活:

* 分层的存储和包管理，devops理念

* 支持多种网络配置


## 常用命令

1. 安装docker

yum install docker 

2. 开启启动

systemctl start/stop docker 启动/关闭docker

systemctl enable docker 开机启动

systemctl disable docker 关闭开机启动

3. docker命令

1) docker version 查看版本

2) docker search 

3) docker pull

4) 运行  docker run --name container-name -d image name  --name :自定义容器名

eg:docker run -name myredis -d redis  -d:后台运行

image name:指定镜像模板

5) 列表  docker ps (查看运行中的容器) ; 

加上a;可以查看所有容器停止 
 
docker stop container-name/container-id  停止当前你运行的容器启动  

docker  start container-name/container id  启动容器

6) 删除  docker rm container-id  删除指定容器

eg：docker rm eb6263750702

7) 端口映射  -p 6379:6379  -p:主机端口(映射到)容器内部的端口

eg:docker run -d -p 6379:6379 -name myredis docker.io/redis

8)  容器日志docker logs container name/container id

9)docker images 查看所有下载的镜像

10)docker rmi dlac13423d3c 删除镜像

## 常见软件运行

工作在前台的至少一个守护进程

1. elasticsearch

docker pull registry.docker-cn.com/library/elasticsearch

运行，默认加载2G内存，所以要设置内存大小9200 端口 9300分布式端口

docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name ES01 a62bcf85821b

springboot 和elastic版本不匹配，安装另外版本

docker pull registry.docker-cn.com/library/elasticsearch:5.5

docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9201:9200 -p 9301:9300 --name ES02 20f2af19bab6

2. mongodb

docker run -d -p 27017:27017 --name mongo ff6df1473b5e

3. python

docker run -d -p 5000:5000 --name python a5b7afcfdcc8

4. rabbitmq

https://hub.docker.com/r/library/rabbitmq/tags/

加速下载

docker pull registry.docker-cn.com/library/rabbitmq:3-management-alpine

启动rabbitmq

docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitmq d39f7aefe7a2

网页访问地址 guest guest

http://192.168.10.8:15672/

5. zookeeper

https://hub.docker.com/r/library/zookeeper/tags/

docker pull registry.docker-cn.com/library/zookeeper 

docker run --name zk01 -p 2181:2181 --restart always -d 3621823e2780

## DockerPile

Dockerfile是一种被Docker程序解释的脚本, Dockerfile 由一条条的指令组成， 每条指令对应Linux 下面

的一条命令。Docker程序将这些Dockerfile指令翻译真正的Linux 命令。

Dockerfile有自己书写格式和支持的命令，Docker程序解决这些命令间的依赖关系，类似于Makefile. Docker程序将读取Dockerfile, 根据指令生成定制的image

最大不超过128层

1、Dockerfile, 需要定义一个Dockerfile， Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、
动态链接库、操作系统的发行版、服务进程和内核进程(当应用进程需要和系统服务和内核进程打交道，这时需要考虑如何设计namespace的权限控制)等等;

2、 Docker镜像， 在用Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像， 当运行Docker镜像时， 会真正开始提供服务。

3、Docker容器，容器是直接提供服务的。

## 其他 

docker build -f .\Dockerfile -t yan/centos:5.0 .

docker commit -m='my first commit' -a='vyvyan' fee031f16de6 yan/centos

docker run -it -v D:/docker:/docker centos /bin/bash

docker rm -f $(docker ps -a -q)


进入docker-compose 容器

docker exec -it 288b1ce5f869 /bin/bash

![](img/77.png) 


https://blog.csdn.net/shykevin/article/details/105503596


## 通过Maven构建Docker镜像

* 准备工作
  * 提供一个Dockerfile
  * 配置dockerfile-maven-plugin插件
* 执行构建
  * mvn package
  * mvn dockerfile:build
* 检查结果
  * docker images










