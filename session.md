# Session

## 1、关于session共享问题

Http协议是无状态的

html/css/js

jsp/servlet / cgi

cookie

session

1.基于session复制

2.基于session统一存储（Nginx + redis 解决session的一致性）

3.基于cookie机制

## 2、关于cookie和session的联系

cookie中会包含哪些信息

名字、值、  过期时间、路径、域 

cookie会带到http请求头中发送给服务器端

如果cookie没有设置过期时间的话，那么cookie的默认生命周期是浏览器的会话

### session机制

1.  session是容器对象，客户端在请求服务端的时候，服务端会根据客户端的请求判断是否包含了sessionid的标识

2. 如果已经包含了，说明该客户端之前已经建立了会话。sessionid是一个唯一的值

3. 如果sessionid不存在，那么服务端会为这个客户端生成一个sessionid。  JSESSIONID

## 3、基于JWT的解决方案

Json Web Token

客户端和服务端信息安全传递以及身份认证的一种解决方案;用在登录上

Jwt的组成

jwt由3个部分组成:

header

typ:"iwt"

alg:"HS256"

payload

jwt本身规范提供的格式

iss

iat

exp

sub

自己定义一些claims

claims

uid

www

Base64去编码

signature

header+ payload组合成-一个字符串

Base64(header).Base64(payload)

JWT的token

str.签名字符串


![](/img/26.png) 




