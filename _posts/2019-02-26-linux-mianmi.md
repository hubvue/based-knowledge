---
title: Linux的免密登录配置
layout: post
subtitle: SSH命令免密登录Linux云服务器配置
date: '2019-02-26 01:16:24'
author: wang chong
catalog: true
header-img: /img/bg/hello_world.jpg
tags:
- Linux
---

### 免密登录的原理
免密登录使用证书进行登录，正式使用加密。
#### 加密的方式
1. 对称加密：加密的过程和解密的过程是一样的，使用加密的算法加密，使用加密算法的逆运算解密。
2. 不对称加密：加密是一种算法，解密是另一种算法。(建立在数学原理上的:质数)

加密用公钥，解密用私钥。免密登录就是不对称加密的方式。

### 配置免密登陆的步骤
#### 生成秘钥对
> ssh-keygen -t rsa -C ‘你自己的名字’ -f '你自己的名字_rsa'

使用上面命令生成秘钥对。
1. -t rsa   -t表示使用什么加密算法进行加密。 rsa是一种加密算法
2.  -C name  在秘钥里面放名字和邮箱都可以。
3.  -f filename_rsa 输出的秘钥文件的文件名

执行完上面命令之后会在本地生成两个文件。
![](https://user-gold-cdn.xitu.io/2019/2/21/1690e6d163e6c4fb?w=558&h=45&f=png&s=11028)
可以看到我的连个test开头的文件。带.pub的是公钥，不带的是私钥。

#### 上传配置公钥
上传配置公钥分为两步：

**1.上传公钥到服务器对应账号的host路径下的.ssh/中。**
> ssh-copy-id -i '公钥文件名' 用户名@服务器ip

- -i指定公钥文件名

**2.配置公钥文件访问权限为600.**
> chmod 600 公钥文件名

#### 配置本地私钥
首先把第一步生成的私钥复制到你的home目录下的.ssh/的路径下。然后配置私钥访问权限为600。
> chmod 600 私钥文件名

如果不配配置文件 必须手动指定私钥要不然登录还是需要密码 
> ssh -i 私钥路径+文件名(最好是绝对路径) 用户名@服务器ip

例如：ssh -i /root/.ssh/test_rsa root@39.105.106.168

#### 免密登陆的本地配置文件
编辑自己home目录的.ssh/路径下的config文件，如果没有这个文件就创建一个。然后配置config文件的访问权限为644。
> chmod 644 config

#### 配置文件
配置文件中分为单主机配置和多主机配置。
1.  单主机配置：一个密钥对用在一个服务器上
2.  多主机配置：同一个密钥对用在多个服务器上。

##### 单主机配置文件。
```
Host ：为连接的服务器起的一个别名
User ：登录服务器的用户名
HostName ：所要连接的服务器的ip或者域名
IdentityFile：私钥文件的路径+文件名
Protocol：协议版本号 默认2
Compression yes     
ServerAliveInterval 60  超时时间，心跳检测
ServerAliveCountMax 20      服务器最大连接数。
LogLevel INFO               日志等级
```
##### 多主机配置文件
```
Host name1
HostName 所要连接的服务器的ip或者域名
Port 22
Host name2
HostName 所要连接的服务器的ip或者域名
Port 22
Host name3
HostName 所要连接的服务器的ip或者域名
Port 22

Host ：*-name   凡是带-name的都走这个规则
User ：登录服务器的用户名
IdentityFile：私钥文件的路径+文件名
Protocol：协议版本号 默认2
Compression yes     
ServerAliveInterval 60  超时时间，心跳检测
ServerAliveCountMax 20      服务器最大连接数。
LogLevel INFO               日志等级
```