# 微服务架构实战

## OAuth2

### OAuth2解决问题域和场景

* 开放系统间授权
  * 社交联合登录
  * 开放API平台

* 现代微服务安全
  * 单页浏览器App ( HTML5/JS/无状态)
  * 无线原生App
  * 服务器端WebApp
  * 微服务和API间调用

* 企业内部应用认证授权( IAM/SSO )

### 什么是OAuth 2.0

* 用于REST/APIs的代理授权框架(delegated authorization framework)

* 基于令牌Token的授权,在无需暴露用户密码的情况下,使应用能获取对用户数据的有限访问权限事实上的标准安全框架

* 解耦认证和授权

* 支持多种用例场景
  * 服务器端WebApp
  * 浏览器单页SPA
  * 无线/原生App
  * 服务器对服务器之间
  
### OAuth 2.0优势

* OAuth 2.0比OAuth 1.0易于实现
* 广泛传播并被持续采用
* 资源服务器和授权服务器解耦
* HTTP/JSON友好易于请求和传递token
* 客户可以具有不同的信任级别
* 更安全，客户端不接触用户密码,服务器端更易集中保护
* 短寿命和封装的token
* 集中式授权,简化客户端
* 考虑多种客户端架构场景

### OAuth 2.0不足

* 协议框架太宽泛,造成各种实现的兼容性和互操作性差
* 和OAuth 1.0不兼容
* OAuth 2.0不是一个认证协议OAuth 2.0本身并不能告诉你任何用户信息

### OAuth术语

* 客户应用(Client Application)
  * 通常是一个Web或者无线应用,它需要访问用户的受保护资源
* 资源服务器(Resource Server)
  * 是一个web站点或者web serviceAPI ,用户的受保护数据保存于此
* 授权服务器(Authorized Server)
  * 在客户应用成功认证并获得授权之后,向客户应用颁发访问令牌Access Token
* 资源拥有者(Resource Owner)
  * 资源的拥有人,想要分享某些资源给第三方应用
  
### 四种OAuth 2.0授权类型(Flows)

* 授权码Authorization Code
  * 通过前端渠道客户获取授权码
  * 通过后端渠道,客户使用authorization code去交换access Token和可选的refresh token
  * 假定资源拥有者和客户在不同的设备上
  * 最安全的流程,因为令牌不会传递经过user-agent

* 简化Implicit
  * 适用于公开的浏览器单页应用
  * Access Token直接从授权服务器返回(只有前端渠道)
  * 不支持refresh tokens
  * 假定资源所有者和公开客户应用在同一个设备上
  * 最容易受安全攻击


* 用户名密码Resource Owner Credentials
  * 使用用户名密码登录的应用,例如桌面App
  * 使用用户名/密码作为授权方式从授权服务器上获取access token
  * 一般不支持refresh tokens
  * 假定资源拥有者和公开客户在相同设备上

* 客户端凭证Client Credentials
  * 适用于服务器间通信场景,机密客户代表它自己或者一个用户
  * 只有后端渠道,使用客户凭证获取一个access token
  * 因为客户凭证可以使用对称或者非对称加密,该方式支持共享密码或者证书
  
![](../img/授权类型选择流程.png) 


课后扩展

* 支持刷新令牌Refresh Token
* 使用关系数据库存储令牌和客户信息
* 使用缓存Cache存储令牌提升性能
* 授权服务器和资源服务器拆分
* Revoke端点
* Introspection端点

* 使用Spring Security OAuth2
* 客户端支持授权码模式
* 客户端支持简化/密码/客户端模式
* 客户端支持refresh token

### 访问令牌的类型

* By reference token(透明令牌)
  * 随机生成的字符串标识符, 无法简单猜测授权服务器如何颁发和存储
  * 资源服务器必须通过后端渠道发送回OAuth2授权服务器的令牌检查端点,才能校验令牌.是否有效,并获取claims/scopes等额外信息

* By value token(自包含令牌)
  * 授权服务器颁发的令牌,包含关;于用户或者客户的元数据和声明(claims)
  * 通过检查签名,期望的颁发者(issuer) ,期望的接收人aud(audience) ,或者scope ,资源服务器可以在本地校验令牌，通常实现为签名的JSON WebTokens(JWT)

课后扩展

* 在JWT令牌中增加定制claims
* JWT令牌的非对称签名和校验
* 使用JWE加密/解密JWT令牌

* 支持密码模式

* Spring Social 新浪微博/微信/QQ

### 常见OAuth 2.0安全问题

* 确保HTTPs传输
* 防止泄露客户密码
* 很多输入需要验证
  * CSRF令牌劫持
    * 使用带state参数的CSRF token
    * 以确保OAuth flow的一致性
  * 重定向时泄露授权码或者令牌
    * 明确注册重定向URIs,并确保
    * URI验证
  * 通过切换客户劫持Token
    * 将同一客户和授权方式/token
    * 请求进行绑定


### OpenID Connect

* 基于OAuth2之.上构建的简单身份认证层
* 支持新的签名的id_ token
* UserInfo端点
* 提供一组标识身份的标准的scopes和claims
  * profile
  * email
  * address
  * Phone
* 支持客户自注册,发现,会话管理(SSO)等额外功能
* 增加了2个新的flow

(Identity, Authentication) + OAuth 2.0 = OpenID Connect

### 生产级部署实践

* 业务指标监控
  * 登录次数
  * 授权次数
  * 颁发访问令牌/刷新令牌个数
  * 活跃令牌数
  * 令牌校验次数
  * 令牌吊销次数
  * 注册client数
* 缓存Caching
* 接口调用性能指标
  * authorize
  * token
  * introspect
  * revoke
* HA和水平扩容

### OAuth2/OIDC开源产品

* Redhat Keycloak (Java)
* Apereo CAS (Java)
* IdentityServer (C#)
* OpenId-Connect-Java-Spring-Server

### Spring Security OAuth2


## Apollo(携程)

### 传统应用配置问题

* 主要采用本地文件静态配置
  * 本地静态配置导致在运行时无法动态修改
* 配置散乱格式不标准
  * 有的用xml格式,有的用properties ,有的存DB
* 易引发生产事故
  * 发布的时候容易将非生产的配置带到生产上,引发事故
* 配置修改麻烦,周期长
  * 当部署的服务器很多时,修改配置费时费力
* 配置信息缺少安全审计和版本控制功能
  * 事后无法追溯,谁改的?改了什么?什么时候改的?
  * 当出现问题无法及时回滚
  
### 配置中心解决办法

* 主要采用本地文件静态配置
  * 集中式配置,所有配置信息都存放配置中心
* 易引发生产事故
  * 环境隔离,不同的环境对应不同的配置,互不干扰.
  * 配置错误,可以立即修改, 即时生效
* 配置信息缺少安全审计和版本控制功能
  * 所有修改有历史记录,方便查找谁和时间
  * 可按需回退到历史版本
* 配置散乱格式不标准
  * 配置中心统一管理格式,用户不必关注格式
* 配置修改麻烦,周期长
  * 配置集中一-次修改,实时通知到所有客户端

### 配置核心需求

* 交付件和配置分离
* 抽象标准化
* 集中式
* 高可用
* 实时性
* 治理

### 权限

* 系统管理员拥有所有的权限
* 创建者可以代为创建项目，责任人是默认的项目管理员,一般创建者=责任人
* 项目管理员可以创建Namespace ,集群,管理项目和Namespace权限
* 编辑权限只能编辑不能发布
* 发布权限只能发布不能编辑
* 查看,普通用户可以搜索查看所有项目配置,但不能做相关操作

### 模块介绍

* Config Service
  * 配置获取接口
  * 配置推送接口
  * 服务Apollo客户端
  
* Admin Service
  * 配置管理接口
  * 配置修改、发布接口
  * 服务Portal

* Meta Server
  * Portal通过域名访问Meta Server获取Admin Service服务列表:
  * Client通过域名访问Meta Server获取Config Service服务列表
  * 相当于一个Eureka Proxy
  * 逻辑角色,和Config Service住在一起部署
  
* Eureka
  * 服务注册和发现
  * Config/Admin
  * Service注册并报心跳
  * 和Config Service住在一起部署

* Portal
  * 配置管理界面
  * 通过Meta Server获取AdminService服务列表
  * 客户端软负载

* Client
  * 应用获取配置,实时更新
  * 通过Meta Server获取Config Service服务列表
  * 客户端软负载

### Apollo监控

* 内置支持CAT
  * https://github.com/dianping/cat
  * 客户端+服务器端埋点
  * 自动依赖扫描
* 定制扩展
  * InfluxDB .
  * Prometheus
* 关键指标
  * 接入应用数量
  * 配置项数量
  * 变更和发布数量
  * 推送拉取次数(success/failure)
  * Config Service 
    * 接口性能
    * GC
    * CPU

### 环境部署

* DEV~开发环境
* FAT~测试环境
* UAT~集成环境
* PRO~生产环境

## Spring Cloud Config

### 优势不足

* 优势
  * 配置存储支持Git
  * 和Spring无缝集成
  * 设计简单轻量

* 不足
  * 动态配置能力弱
  * 治理能力弱
  * 不算严格企业级



* Apollo vs Spring Cloud Config

|功能点 |Apollo Spring| Cloud Config|
|:--:|:--:|:--:|
|配置界面 |统一界面管理不同环境/集群配置 |无，通过git操作|
|配置生效时间 |实时 |重启生效，或者Refresh，或git hook + MQ扩展|
|版本管理 |界面上直接提供发布历史和回滚按钮 |无，通过git操作|
|灰度发布 |支持| 不支持|
|授权/审计/审核 |界面上直接操作，且支持修改和发布权限分离 |需要通过git仓库设置，且不支持修改和发布权限分离|
|实例配置监控| 可以方便看到当前哪些客户端在使用哪些配置| 不支持|
|配置获取性能 |快，通过数据库访问+缓存支持 |较慢，需从git clone repo，然后本地文件读取|
|客户端支持| 原生支持Java/.Net，提供API，支持Spring annotation |Spring应用+annotation支持|

## Zuul

### Zuul网关最佳实践

* 异步AsyncServlet优化连接数
* Apollo配置中心集成动态配置
* Hystrix熔断限流
  * 信号量隔离
* 连接池管理
* CAT和Hystrix监控
* 过滤器调试技巧
* 网关无业务逻辑
* 自助路由(需定制扩展)

## 监控

### 应用监控缺失造成的坑

* 线上发布了服务,怎么知道- -切正常?
* 大量报错,到底哪里产生的,谁才是根因?
* 人工配置错误,通宵排错,劳民伤财!
* 应用程序有性能问题,怎么尽早发现问题?
* 数据库问题,在出问题之前能洞察吗?
* “网络问题”成为"最好借口”, 运维如何反击?
* 任何可能出问题的地方都会出错! ! ! (康威定律)
* 微服务需要应用监控! ! !

### 核心概念

* Trace一次分布式调用的链路踪迹
* Span一个方法(局部或远程)调用踪迹
* Annotation附着在Span.上的日志信息
* Sampling采样率

### 资深用户反馈

* 报表丰富,有助于从各个角度了解系统整体状况
* 便于故障快速发现和根因定位
* 有助于培养互联网人的:
  * DevOps自主和自助理念
  * 卓越质量意识( 200ms问题)
* 用于监督和检验:
  * 系统设计,依赖关系的合理性
  * 服务分层治理, 聚合层/业务层/基础层
* 发现技术债
  * 红黑榜

### 生产治理实践

* 框架统一埋点
  * MVC/RPC/Cache/DAL
  * 日志关联TraceId
  * Error log（log4j/logback）集成Cat
* 技术债
  * 红黑榜
  * Matrix/Cross调用和依赖关系合理性
  * 数据库N+1问题
* Metric
  *  推荐使用专门时间序列数据库TSDB(KairosDB/InfluxDB等)作为补充
* 告警
  * 推荐使用专门的告警服务(Zmon)作为补充
* 项目定期分组
* 定期关注State报表和HDFS使用情况


### Spring Cloud Sleuth简介
* Spring Cloud封装的Zipkin兼容客户端Tracer
* 添加traceId和spanId到Slf4J MDC
* 支持埋点的库
  * Hystrix
  *  RestTemplate
  * Feign
  * Messaging with Spring Integration
  * Zuul

*  支持采样策略
* 老版本基于HTrace，最新版本基于Brave
* 依赖
  * spring-cloud-sleuth-zipkin
  * spring-cloud-sleuth-stream
  
## Hystrix


### 线程Vs信号量隔离

信号量隔离

* 优点
  * 轻量,无额外开销
* 不足
  * 不支持任务排人和主动超时
  * 不支持异步调用
* 适用
  * 受信客户
  * 高扇出(网关)
  * 高频高速调用(cache)

线程池隔离

* 优点
  * 支持排队和超时
  * 支持异步调用
* 不足
  * 线程调用会产生额外的开销
* 适用
  * 不受信客户
  * 有限扇出

## Spring Cloud Eureka

### 服务端主要配置项

|配置参数(eureka.server.*)| 默认值| 说明|
|:--:|:--:|:--:|
|enableSelfPreservation| true| 启用注册中心的自保护机制, Eureka如果统计到15分钟之内损失> 15%的微服务心跳,则将会触发自保护机制,不再剔除服务提供者。|
|waitTimeInMsWhenSyncEmpty| 1000*60*5| 在Eureka服务器获取不到集群里对等服务器上的实例时,需要等待的时间,单位为毫秒,默认为1000 * 60* 5。单机开发模式建议设置为0。|

### HealthCheckHandler
* 定制注册
  * EurekaClient#registerHealthCheck
* Spring Cloud
  * `eureka.client.healthcheck.enabled` =true
  * EurekaHealthCheckHandler
    * DiskSpaceHealthIndicator
    * RefreshScopeHealthIndicator
    * HystrixHealthIndicator





















