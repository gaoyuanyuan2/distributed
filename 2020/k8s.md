# Kubernetes

* 容器管理与调度编排领域的首选平台和事实标准

## 应用部署运行模式变迁

物理单机->虚拟化（容器化）-> 云原生

## 云原生

* 随着容器技术的出现以及应用所面临的外部环境的变化，云原生逐渐成为一种应用云化开发、部署和运行的主流方式

* 基础前提：应用的容器化和微服务化。容器，作为应用部署、运行和管理的基本单元

* 核心：借助容器管理制度化平台进行动态编排和资源优化利用


## 容器编排管理平台

Kubernetes 作为容器平台提供：

* 以Pod（容器组）为基础的编排和调度单元以及声明式的对象配置模型
（控制器、configmap、secret等）

* 资源配额与分配管理

* 健康检查、自愈、伸缩与滚动升级

## 微服务支撑平台

Kubernetes 提供了对微服务的支撑：

* 服务发现、服务编排月内部路由支持

* 服务快速部署和自动负载均衡

* 提供对“有状态”服务的支持等

## 优点

从云应用角度，Kubernetes带来的好处：

* 容器管理、调度和编排的事实标准：摆脱锁定，支持跨云

* 先进的Workload管理之经验模型：Pod和Controllers

* 原生支持微服务抽象：服务注册、服务发现和自动负载均衡

## Master

Kubernetes集群大脑，控制平面：

* 所有集群的控制命令都传递给Master组件并在其上执行

* 每个Kubernetes集群至少有一套Master组件(当前默认:一个)

* 每套master组件包括三个核心组件(apiserver, scheduler
和controller-manager)以及集群数据配置中心etcd

## 组件: API Server

* 集群控制的唯一入口 , 是提供Kubernetes集群控制RESTful API的核心组件

* 集群内各个组件之间数据交互和通信的中枢

* 提供集群控制的安全机制(身份认证、授权以及admission control)

## 组件: Scheduler

* 通过API Server的Watch接口监听新建Pod副本信息,并通过调度算法为该Pod选择一个最合适的Node

* 支持自定义调度算法provider

* 默认调度算法内置预选策略和优选策略,决策考量资源需求、服务质量、软硬件约束、亲缘性、数据局部性等指标参数

## 组件: ControllerManager

* 集群内各种资源controller的核心管理者

* 针对每一种具体的资源,都有相应的Controller

* 保证其下管理的每个Controller所对应的资源始终处于"期望状态”

## Node:工作负载节点

Node : Kubernetes集群中真正的工作负载节点

* Kubernetes集群由多个Node共同承担工作负载, Pod被分配到某个具体的Node上执行

* kubernetes通过node controller对node资源进行管理。支持动态在集群中添加或删除Node

* 每个集群Node上都会部署Kubelet和Kube-proxy两个组件

## 组件: Kubelet

* 位于集群中每个Node.上的非容器形式的服务进程组件, Master和node之间的桥梁

* 处理Master下发到本Node.上的Pod创建、启停等管理任务;向API Server注册Node信息

* 监控本Node.上容器和节点资源情况,并定期向Master汇报节点资源占用情况

## 组件: Kube-proxy

Kube-proxy :运行在每个Node.上

* Service抽象概念的实现 ,将到Service的请求按策略(负载均衡)算法分发到后端Pod(Endpoint)上

* 默认使用iptables mode实现
  
* 支持nodeport模式 ,实现从外部访问集群内的service

## 小结

Kubernetes架构和组件:

* 两类节点: Master和Node

* Master组件: apiserver, scheduler, controllerManager, etcd

* 节点组件 : kubelet, kube- proxy

## Kubernetes对象

 Kubernetes对象:是一-种持久化的、用于表示集群状态的实体

 * 一种声明式的意图的记录,一般使用yam|文件描述对象

 * Kubernetes集群使用Kubernetes对象来表示集群的状态

 * 通过API/kubectl管理Kubernetes对象


命令式编程:它侧重于如何实现程序，就像我们刚接触编程的时候那样，我们需费把程序的实现过程按照逻辑结果一步步写下来
声明式编程:它侧重于定义想要什么，然后告诉计算机/引擎，让他帮你去实现（sql）



|概念|作用|
|:--:|:--:|
|Cluster|超大计算机抽象，由节点组成|
|Container|应用居住和运行在容器中|
|Pod|Kubernetes基本调度单位|
|ReplicaSet|创建和管理Pod,支持无状态应用|
|Service|应用Pods的访问点，屏蔽IP寻址和负载均衡|
|Deployment|管理ReplicaSet,支持滚动等高级发布机制|
|ConfigMap/Secrets|应用配置，secret 敏感数据配置|
|DaemonSet|保证每个节点有且仅有一一个Pod,常见于监控|
|StatefulSet |类似ReplicaSet,但支持有状态应用|
|Job|运行一次就结束的任务|
|CronJob|周期性运行的任务|
|Volume|可装载磁盘文件存储|
|PersistentVolume/PersistentVolumeClaims|超大磁盘存储抽象和分配机制|
|Label/Selector|资源打标签和定位机制|
|Namespace|资源逻辑隔离机制|
|Readiness Probe|就绪探针，流量接入Pod判断依据|
|Liveness Probe|存活探针，是否kill Pod的判断依据|

### 网络
|对比项|作用|实现|
|:--:|:--:|:--:|
|节点网络|Master/Worker节点之间网络互通|路由器，交换机，网卡|
|Pod网络|Pod之间互通|虚拟网卡，虚拟网桥，路由器|
|Service网络|屏蔽Pod地址变化+负载均衡|Kube-proxy, Netfilter, Api-Server, DNS|
|NodePort|将Service暴露在节点网络上|Kube-proxy + Netfliter|
|LoadBalancer|将Service暴露在公网上+负载均衡|公有云LB + NodePort|
|Ingress|反向路由，安全，日志监控 (类似反向代理or网关)|Nginx/Envoy/Traefik/Faraday|

安装

https://github.com/AliyunContainerService/k8s-for-docker-desktop

校验Kubernetes安装
1. kubectl version
2. kubectl config current-context
3. kubectl cluster-info
4. kubectl get nodes

命令行校验
kubectl get pods -0 wide
Kubectl get services
Kubectl get deployments

清理
kubectl delete deployments - -all
kubectl delete services - -all
kubectl delete configmaps - -all


   











