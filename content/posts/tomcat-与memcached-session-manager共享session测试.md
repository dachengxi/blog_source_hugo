---
title: tomcat与memcached-session-manager共享session测试
date: 2016-07-01 15:09:23
categories: 动手做
tags:
- msm
- memcached-session-manager
- session共享
---

## 简介
看书刚好看到集群session共享,总觉得只看不做,不能确定这到底是怎么运行的.所以就做了这个测试.有关[Memcached-Session-Manager](https://github.com/magro/memcached-session-manager),Memcached,以及集群session共享等相关知识,请自行补充.本次就简单记录下测试过程.
有关其他的方式以及其他的情况,本文不做说明,有需要的话,会再写其他情况和方式下的文章.

<!-- more -->

## 环境说明
* 本机OSX 10.11,tomca7.0.57,memcached-1.4.24
* 虚拟机Ubuntu16.04,tomca7.0.57,memcached-1.4.24
* 使用non-sticky sessions（非粘性session）
* 序列化:使用默认,java进行序列化，由memcached-session-manager.jar这个jar包来提供方法.
* 有关粘性和非粘性的区别以及序列化等不做解释.

## 具体步骤

### 安装jdk,memcached,tomcat
不做详细说明

### 放jar包
将如下相关jar包分别放置到两台机器的tomcat  `$CATALINA_HOME/lib/`目录中.

* [memcached-session-manager-${version}.jar](http://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager/)
* [memcached-session-manager-tc7-${version}.jar](http://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager-tc7/)
* [spymemcached-2.11.1.jar](http://repo1.maven.org/maven2/net/spy/spymemcached/2.11.1/spymemcached-2.11.1.jar)

### 修改tomcat配置文件
两台机器分别修改tomcat `$CATALINA_HOME/conf/context.xml`文件,添加如下代码到Context节点下:

```
<Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
        memcachedNodes="n1:192.168.110.197:11211,n2:192.168.110.198:11211"
        sticky="false"
        sessionBackupAsync="false"
        requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"
 />
```
### 部署Web项目到tomcat
新建测试用的web项目,并部署到两台tomcat中.测试代码简单如下:

```
<body>
	Session ID:<%=session.getId()%> <br>
	IP:<%=request.getServerName()%> <br>
	Port:<%=request.getServerPort()%>
</body>
```

### 启动两台机器的memcached
```
memcached -m 32 -p 11211 -d
```

### 启动两台机器的tomcat
查看tomcat信息`tail -f catalina.out`未报错,看到类似如下信息就启动成功

```
信息: --------
-  finished initialization:
- sticky: false
- operation timeout: 1000
- node ids: [n1, n2]
- failover node ids: []
- storage key prefix: null
--------

```

### 访问测试页面
分别访问两台机器的测试页面:

* 同一个浏览器
* 两个浏览器
* 结束掉一个机器的memcached进程在访问等等

同一个浏览器不同标签页访问192.168.110.197和192.168.110.198 得到的sessionid都是一样的:

```
Session ID:39D5E175513B4496C136F5E1554478CD-n1 
IP:192.168.110.197 
Port:8080

Session ID:39D5E175513B4496C136F5E1554478CD-n1 
IP:192.168.110.198 
Port:8080
```
关闭ip为197的memcached进程之后,刷新页面:
```
Session ID:39D5E175513B4496C136F5E1554478CD-n2 
IP:192.168.110.197 
Port:8080

Session ID:39D5E175513B4496C136F5E1554478CD-n2 
IP:192.168.110.198 
Port:8080
```
测试成功.

## 参考
[https://github.com/magro/memcached-session-manager/wiki/SetupAndConfiguration#introduction](https://github.com/magro/memcached-session-manager/wiki/SetupAndConfiguration#introduction)

[http://laoxu.blog.51cto.com/4120547/1566477](http://laoxu.blog.51cto.com/4120547/1566477)



