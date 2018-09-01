## Session
### 1、关于session共享问题
<br>Http协议是无状态的
<br>html/css/js
<br>jsp/servlet / cgi
<br><br>cookie
<br>session
<br><br>1.基于session复制
<br><br>2.基于session统一存储
<br><br>3.基于cookie机制

### 2、关于cookie和session的联系
<br>cookie中会包含哪些信息
<br>名字、值、  过期时间、路径、域 
<br>cookie会带到http请求头中发送给服务器端
<br>如果cookie没有设置过期时间的话，那么cookie的默认生命周期是浏览器的会话
#### session机制
<br>1.  session是容器对象，客户端在请求服务端的时候，服务端会根据客户端的请求判断是否包含了sessionid的标识
<br><br>2. 如果已经包含了，说明该客户端之前已经建立了会话。sessionid是一- 个唯一的值
<br><br>3. 如果sessionid不存在，那么服务端会为这个客户端生成一- 个sessionid。  JSESSIONID

### 3、基于JWT的解决方案
<br>Json Web Token
<br>客户端和服务端信息安全传递以及身份认证的一种解决方案;用在登录上
<br>Jwt的组成
<br>jwt由3个部分组成:
<br><br>header
<br>typ:"iwt"
<br>alg:"HS256"
<br><br>payload
<br>jwt本身规范提供的格式
<br>iss
<br>iat
<br>exp
<br>sub
<br><br>自己定义一些claims
<br>claims
<br>uid
<br>www
<br>Base64去编码
<br>signature
<br>header+ payload组合成-一个字符串
<br>Base64(header).Base64(payload)
<br>JWT的token
<br>str.签名字符串
<br><br>
![](https://github.com/gaoyuanyuan2/distributed/blob/master/img/26.png) 




