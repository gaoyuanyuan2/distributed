# Docker
## Docker 是什么
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
## 优点:
<br>资源利用率高
<br>高效性
<br>易于扩展、伸缩
<br> Go to Product快速
<br>一致性
<br>可封装性
<br>隔离性
## 常用命令
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
## 常见软件安装
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
## Service Mesh为:
<br>专用基础设施层:独立的运行单元。
<br>包括数据层和控制层:数据层负责交付应用请求，控制层控制服务如何通讯。轻量级透明代理:实现形式为轻量级网络代理。
<br>处理服务间通讯:主要目的是实现复杂网络中服务间通讯。
<br>可靠地交付服务请求:提供网络弹性机制，确保可靠交付请求。
<br>与服务部署一起，但服务无需感知:尽管跟应用部署在-起，但对应用是透明的。
## Service Mesh能做什么
<br>Service Mesh作为透明代理，它可以运行在任何基础设施环境，而且跟应用非常靠近，那么，Service Mesh能做什么呢?
<br><br>负载均衡:运行环境中微服务实例通常处于动态变化状态，而且经常可能出现个别实例不能正常提供服务、处理能力减弱、卡顿等现象。但由于所有请求对Service 
Mesh来说是可见的，因此可以通过提供高级负载均衡算法来实现更加智能、高效的流量分发，降低延时，提高可靠性。
<br><br>服务发现:以微服务模式运行的应用变更非常频繁，应用实例的频繁增加减少带来的问题是如何精确地发现新增实例以及避免将请求发送给已不存在的实例变得更加复杂。Service Mesh可以提供简单、统一、 平台无关的多种服务发现机制，如基于DNS, KN键值对存储的服务发现机制。
<br><br>熔断:动态的环境中服务实例中断或者不健康导致服务中断可能会经常发生，这就要求应用或者其他工具具有快速监测并从负载均衡池中移除不提供服务实例的能力，这种能力也称熔断，以此使得应用无需消耗更多不必要的资源不断地尝试，而是快速失败或者降级，甚至这样可避免一些潜在的关联性错误。而Service Mesh可以很容易实现基于请求和连接级别的熔断机制。
<br><br>动态路由:随着服务提供商以提供高稳定性、高可用性以及高SLA服务为主要目标，为了实现所述目标，出现各种应用部署策略尽可能从技术手段达到无服务中断部署，以此避免变更导致服务的中断和稳定性降低，例如: 
Blue/Green部署、Canary部署，但是实现这些高级部署策略通常非常困难。关于应用部署策略，可参考Etienne 
Tremel的文章，他对各种部署策略做了详细的比较。而如果运维人员可以轻松的将应用流量从staging环境到生产线环境，一个版本到另外一个版本，更或者从一个数据中心到另外一个数据中心进行动态切换，甚至可以通过一个中心控制层控制多 
少比例的流量被切换。那么Service Mesh提供的动态路由机制和特定的部署策略如Blue/Green部署结合起来，实现上述目标更加容易。
<br><br>安全通讯:无论何时，安全在整个公司、业务系统中都有着举足轻重的位置，也是非常难以实现和控制的部分。而微服务环境中，不同的服务实例间通讯变得更加复杂，那么如何保证这些通讯是在安全、授权情况下进行非常重要。通过将安全机制如TLS加解密和授权实现在Service Mesh上，不仅可以避免在不同应用的重复实现，而且很容易在整个基础设施层更新安全机制，甚至无需对应用做任何操作。多语言支持:由于Service Mesh作为独立运行的透明代理，很容易支持多语言。多协议支持:同多语言支持一样，实现多协议支持也非常容易。
<br><br>指标和分布式追踪: Service Mesh对整个基础设施层的可见性使得它不仅可以暴露单个服务的运行指标，而且可以暴露整个集群的运行指标。
<br><br>重试和最后期限: Service Mesh的重试功能避免将其嵌入到业务代码，同时最后期限使得应用允许一个请求的最长生命周期，而不是无休止的重试。
















