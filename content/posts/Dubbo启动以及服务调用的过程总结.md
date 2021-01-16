---
title: Dubbo启动以及服务调用的过程总结
date: 2020-03-20 23:25:44
categories: 
- dubbo
tags:
- dubbo
---
服务暴露过程、服务引用过程、服务调用过程、消费者调用底层通信过程、提供者接受请求底层通信过程简单总结。

<!--more-->

# 服务暴露过程
服务暴露、服务提供者初始化

服务转化成Invoker -> Invoker转化成Exporter -> Transporter使用具体的Server启动服务监听端口 -> 使用具体的Registry将服务注册到注册中心。

开始暴露服务，调用ServiceConfig的export方法导出服务，ServiceConfig使用ProxyFactory将我们要暴露的服务转化成一个Invoker。服务转化成Invoker后，需要通过具体的协议（比如Dubbo）将Invoker转化成Exporter（比如DubboExporter）。Exporter中使用Transporter来初始化一个具体的Server（比如Netty），并绑定服务端口，此时服务就被暴露出去了。服务暴露之后会调用具体的Registry（比如Zookeeper）将服务注册到注册中心去。

![export](/Dubbo启动以及服务调用的过程总结/dubbo-export.jpg)

# 引用服务过程
引用服务、消费者初始化、消费者订阅服务

服务订阅 -> 服务转化成Invoker -> Transporter使用具体的Client启动服务准备连接 -> 使用Cluster并继续初始化Invoker -> 封装成一个代理返回。

开始引用服务，调用ReferenceConfig的get方法引用服务，ReferenceConfig调用RegistryProtocol并使用具体的Registry（比如Zookeeper）来订阅服务，Registry会通知Directory开始引用服务（异步），也就是将要引用的服务转化成一个Invoker。Directory会使用具体的Protocol（如Dubbo）将引用的服务转化成一个Invoker。Invoker中使用Transporter来初始化一个具体的Client（比如Netty）用来准备和服务端提供者进行通信。RegistryProtocol调用Cluster的合并方法来初始化Invoker，然后ReferenceConfig在Invoker生成之后返回一个服务的代理。

![refer](/Dubbo启动以及服务调用的过程总结/dubbo-refer.jpg)

# 服务调用过程
服务调用过程分为两部分：服务消费者调用服务和服务提供者接受服务请求。

## 服务消费者调用服务
获取到代理 -> 调用Invoker -> Exchange调用远程服务

服务开始调用，首先获取到在服务引用过程中生成的代理，获取到代理后先执行一些过滤器链，比如：缓存、mock等等。接下来会根据Cluster来选择一个具体的Invoker，比如：failover、failsave、failfast、failback、forking、broadcast等，同时使用Directory从Registry中获取所有的invokers，然后使用LoadBalance（random、roundRobin、leastActive、consistentHash）选中具体调用的服务。选中服务之后会先执行过滤器链，再使用具体的Protocol（比如DubboProtocol）调用Transporter并使用具体的Client（比如Netty）来进行服务的调用。发送的请求会进行Codec编码和Serialzation序列化。

## 服务提供者接受服务请求

服务提供者接收到请求后，会进行反序列化和Decodec解码，然后从线程池中获取一个线程交给具体的Server（比如Netty)进行处理，然后会交给具体的Protocol（比如Dubbo）来根据参数获取具体的Exporter，继续执行一系列的过滤器链，然后使用ProxyFactory来获取具体的Invoker（比如Dubbo），Invoker就会调用真正的服务实现类，然后将结果返回。

![invoke](/Dubbo启动以及服务调用的过程总结/dubbo-extension.jpg)

![invoke](/Dubbo启动以及服务调用的过程总结/dubbo-invoke.png)

processon源文件：[dubbo-invoke.pos](/Dubbo启动以及服务调用的过程总结/dubbo-invoke.pos)

# 底层通信过程
服务消费者发送请求底层通信过程和服务提供者接受服务请求底层通信过程

稍后添加
