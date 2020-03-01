# Spring Cloud 

## 服务注册和服务发现

### 对比

1、Sping Cloud Eureka

优点：
* CAP：AP,数据最终一致性
* 简单易用：开箱即用，控制台管理

缺点：
* 内存限制 
* 单一调度更新：客户端简单轮训更新，增加服务器压力
* 集群伸缩性限制：广播复制模型，增加服务器压力


2、Spirng Cloud Zookeeper

优点：
* 成熟协调协系统
* CAP理论:CP

缺点：
* 维护成本
* 伸缩性限制

3、Spring Cloud Consul

优点：
* 通用方案：适用于Service Mesh Java 生态
* CAP:AP模型

缺点：
* 可靠性无法保证
* 非Java 生态

|技术选型|CAP模型|适用规模（建议）|控制台管理|社区活跃|
|:--:|:--:|:--:|:--:|:--:|
|Eureka|AP|<30k|支持|低|
|Zookepper|CP|<20k|不支持|中|
|Consul|AP|<5K|支持|高|