## Docker
### 1、Docker 是什么
<br>1. Docker是一个开源的应用容器引擎,基于Go语言并遵从Apache2.0协议开源。Docker可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中,然后发布到任何流行的Linux机器上,也可以实现虚拟化。
<br>容器是完全使用沙箱机制,相互之间不会有任何接口，更重要的是容器性能开销极低。
 <br>Docker支持将软件编译成一个镜像 ;然后在镜像中各种软件做好配置,将镜像发布出去,其他使用者可以直接使用这个镜像。运行中的这个镜像称为容器，容器启动是
非常快速的。类似windows里面的ghost操  docker作系统,安装好后什么都有了。
<br><br>2. Docker核心概念
<br><br>1) docker镜像(lmages) : Docker镜像是用于创建Docker容器的  Clients  Hosts  Registries模板。
<br><br>2) docker容器(Container) :容器是独立运行的一个或一组应用。 
<br><br>3) 客户端(Client) :客户端通过命令行或者其他工具使用 Docker  APl与Docker的守护进程通信 
<br><br>4) docker主机(Host) :一个物理或者虚拟的机器用于执行Docker守护进程和容器。
<br><br>5) docker仓库(Registry) : Docker仓库用来保存镜像,可以理解为代码控制中的代码仓库。
<br><br>6) DockerHub(tts://hub.docker.com)提供了庞大的镜像集合供使用。
### 2、常用命令
<br>1. 安装docker
<br>yum install docker 
<br><br>2. 开启启动
<br>systemctl start/stop docker 启动/关闭docker
<br>systemctl enable docker 开机启动
<br>systemctl disable docker 关闭开机启动
<br><br>3. docker命令
<br><br>1) docker –v 查看版本
<br><br>2) docker search 
<br><br>3) docker pull
<br><br>4) 运行  docker run --name container-name -d image name  --name :自定义容器名
<br>eg:docker run -name myredis -d redis  -d:后台运行
<br>image name:指定镜像模板
<br><br>5) 列表  docker ps (查看运行中的容器) ; <br> 加上a;可以查看所有容器停止 <br> docker stop container-name/container-id  停止当前你运行的容器启动  
<br>docker  start container-name/container id  启动容器
<br><br>6) 删除  docker rm container-id  删除指定容器
<br>eg：docker rm eb6263750702
<br><br>7) 端口映射  -p 6379:6379  -p:主机端口(映射到)容器内部的端口
<br>eg:docker run -d -p 6379:6379 -name myredis docker.io/redis
<br><br>8)  容器日志docker logs container name/container id
### 3、常见软件安装
<br>1. elasticsearch
<br>docker pull registry.docker-cn.com/library/elasticsearch
<br>运行，默认加载2G内存，所以要设置内存大小9200 端口 9300分布式端口
<br>docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9200:9200 -p 9300:9300 --name ES01 a62bcf85821b
<br>springboot 和elastic版本不匹配，安装另外版本
<br>docker pull registry.docker-cn.com/library/elasticsearch:5.5
<br>docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9201:9200 -p 9301:9300 --name ES02 20f2af19bab6
<br><br>2. mongodb
<br>docker run -d -p 27017:27017 --name mongo ff6df1473b5e
<br><br>3. python
<br>docker run -d -p 5000:5000 --name python a5b7afcfdcc8
<br><br>4. rabbitmq
<br>https://hub.docker.com/r/library/rabbitmq/tags/
<br>加速下载
<br>docker pull registry.docker-cn.com/library/rabbitmq:3-management-alpine
<br>启动rabbitmq
<br>docker run -d -p 5672:5672 -p 15672:15672 --name myrabbitmq d39f7aefe7a2
<br>网页访问地址 guest guest
<br>http://192.168.10.8:15672/
<br><br>5. zookeeper
<br>https://hub.docker.com/r/library/zookeeper/tags/
<br>docker pull registry.docker-cn.com/library/zookeeper
<br>docker run --name zk01 -p 2181:2181 --restart always -d 3621823e2780
















