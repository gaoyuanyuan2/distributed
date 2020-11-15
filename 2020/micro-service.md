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
  * 发布的时候容易将非生产的配置带到生产上,弓|发事故
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



















