## MongoDB
### 1、什么是MongoDB
<br>1.  没有表结构的概念,每条记录可以有完全不同的结构
<br><br>2.  业务开发方便快捷
<br><br>3.  完全的索引支持
<br><br>4.  方便的冗余与扩展
<br><br>1) 复制集保证数据安全
<br><br>2) 分片扩展数据规模
### 2、索引
<br>1.  多键索引与单键索引创建形式相同，区别在于字段的值。
 <br>   单键索引:值为一个单一的值, 例如字符串,数字或者日期。多键索引:值具有多个记录，例如数组。
<br><br>2.  过期索引
<br>这适合存储一些在一段时间之后会失效的数据比如用户的登录信息、存储的日志。
<br><br>3.  全文索引
<br>全文索引还不支持中文
<br><br>4.  地理位置索引
<br><br>1)  查找距离某个点一定距离内的点；
<br><br>2)  查找包含在某区域内的点。
<br><br>5.  2d索引
<br>平面地理位置索引
<br><br>6.  2dsphere索引概念:球面地理位置索引 
### 3、MongoDB安全
<br><br>1.  最安全的是物理隔离:不现实
<br><br>2.  网络隔离其次
<br><br>3.  防火墙再其次
<br><br>4.  用户名密码在最后
### 4、MongoDB创建用户
<br><br>1.  创建语法: createUser ( 2.6之前为addUser )
<br><br>2.  角色类型:内建类型( read,readWrite,dbAdmin,dbOwner,userAdmin )
### 5、MongoDB用户角色详解
<br>1.数据库角色( read,readWrite,dbAdmin,dbOwner,userAdmin )
<br><br>2.集群角色( clusterAdmin,clusterManager... )
<br><br>3.备份角色( backup,restore... )
<br><br>4.其他特殊权限( DBAdminAnyDatabase... )

