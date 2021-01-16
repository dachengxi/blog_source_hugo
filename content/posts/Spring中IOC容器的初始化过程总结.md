---
title: Spring中IOC容器的初始化过程总结
date: 2020-03-22 21:16:14
categories: Spring
tags:
- Spring
---

总结一下Spring IOC容器的初始化过程，对于具体的源码分析以前写过文档，网上也有很多类似的文章，这里不做重复。

<!--more-->

Spring IOC容器初始化过程总得说起来也简单，基本是整个Spring启动的过程。大体流程：

> Spring启动 -> 加载配置文件 -> 将配置文件转化成Resource -> 从Resource中解析转换成BeanDefinition -> 主动或则被动触发Bean的初始化过程 -> 应用程序中使用Bean -> 销毁Bean -> 容器关闭。

其实总的流程就这么简单，但是具体实现有很多很多的细节需要处理，讲完就是一本书。这里就是简单总结，所以内容会很少很少。

1. Spring启动。
2. 加载配置文件，xml、JavaConfig、注解、其他形式等等，将描述我们自己定义的和Spring内置的定义的Bean加载进来。
3. 加载完配置文件后将配置文件转化成统一的Resource来处理。
4. 使用Resource解析将我们定义的一些配置都转化成Spring内部的标识形式：BeanDefinition。
5. 在低级的容器BeanFactory中，到这里就可以宣告Spring容器初始化完成了，Bean的初始化是在我们使用Bean的时候触发的；在高级的容器ApplicationContext中，会自动触发那些lazy-init=false的单例Bean，让Bean以及依赖的Bean进行初始化的流程，初始化完成Bean之后高级容器也初始化完成了。
6. 在我们的应用中使用Bean。
7. Spring容器关闭，销毁各个Bean。

这中间还会有很多其他的工作，对于我们从宏观上来了解Spring的整体没有影响，不做过多阐述。



