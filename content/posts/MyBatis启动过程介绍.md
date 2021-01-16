---
title: MyBatis启动过程介绍
date: 2020-11-02 22:31:10
categories: MyBatis
tags: 
- MyBatis
---

MyBatis启动过程学习。

<!--more-->

平时最常使用MyBatis的方式是跟Spring整合使用，把MyBatis交给Spring来管理。不管怎么交给Spring，都还是使用MyBatis的最基础的方式来启动的。

一般步骤：

1. 先定义一个mybatis-config.xml配置文件
2. 定义具体的mapper.xml文件以及对应Mapper接口
3. 使用SqlSessionFactoryBuilder读取mybatis-config.xml文件，并得到一个SqlSessionFactory
4. 使用SqlSessionFactory获取一个SqlSession
5. 使用SqlSession调用具体的Mapper执行增删改查等等

这里要学习的就是第三步，使用SqlSessionFactoryBuilder读取配置文件并得到一个SqlSessionFactory。具体使用时候代码是：`SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(InputStream inputStream) `，这句代码的意思就是使用一个SqlSession工厂构建器，使用配置文件来构建一个SqlSession工厂，这个工厂用来生产SqlSession。

# 创建SqlSessionFactory

SqlSessionFactoryBuilder在创建一个SqlSessionFactory的时候，需要以mybatis-config.xml配置文件作为标准，它不是直接解析配置文件，而是将工作交给XMLConfigBuilder来进行处理，XMLConfigBuilder就是专门用来解析配置文件的，解析完成后会返回一个Configuration给SqlSessionFactoryBuilder，SqlSessionFactoryBuilder根据这个Configuration对象创建一个SqlSessionFactory返回。创建的默认是DefaultSqlSessionFactory。

# XMLConfigBuilder解析配置文件

SqlSessionFactory把配置文件解析交给XMLConfigBuilder来进行，工作就是解析xml配置文件中的节点，并将这个配置文件使用一个Configuration对象来表示。

解析步骤如下：

- 解析properties节点
- 解析settings节点
- 加载自定义VFS实现类
- 加载自定义Log实现类
- 解析typeAliases节点
- 解析plugins节点
- 解析objectFactory节点
- 解析objectWrapperFactory节点
- 解析reflectorFactory节点
- 将解析到的settings节点的内容设置到Configuration中去
- 解析environments节点
- 解析databaseIdProvider节点
- 解析typeHandlers节点
- 解析mappers节点

总结一句就是解析xml配置文件，并设置到Configuration对象中去。

## Configuration对象的来源

XMLConfigBuilder在创建的时候，会首先创建一个Configuration对象：`super(new Configuration())`，这就是Configuration的诞生。

### Configuration对象创建的时候都做了些什么？

简单说，Configuration对象创建的时候，同时创建了很多其他对象。xml配置文件中有很多属性节点等等，这些节点都要一一对应到Configuration对象中，所以Configuration对象中有很多其他对象来表示xml中的节点属性等。

创建的对象大概如下：

- Properties对象
- DefaultReflectorFactory对象，默认的创建和缓存Reflector的工厂
- DefaultObjectFactory对象，默认对象创建工厂
- DefaultObjectWrapperFactory对象，默认ObjectWrapper创建工厂
- JavassistProxyFactory对象，Javassist代理工厂
- MapperRegistry对象，相当于一个Map，用来保存Mapper接口和对应的MapperProxyFactory之间的关系
- InterceptorChain对象，相当于一个List，用来保存所有的拦截器
- TypeHandlerRegistry对象，相当于一个Map，用来保存TypeHandler、Java类型、JDBC类型之间的关系
- TypeAliasRegistry对象，相当于一个Map，用来保存一些常用的类和其别名之间的关系
- LanguageDriverRegistry对象，相当于一个Map，用来保存类和LanguageDriver之间的关系
- 用来保存MappedStatement和对应的id关系的StrictMap对象
- 用来保存缓存id和缓存之间关系的StrictMap对象
- 用来保存ParameterMap和对应id关系的StrictMap对象
- 用来保存ResultMap和对应id关系的StrictMap对象
- 用来保存KeyGenerator和对应id关系的StrictMap对象
- 用来保存SQL Fragment和对应id关系的StrictMap对象
- 同时还向TypeAliasRegistry中注册了很多MyBatis内部的类的一些别名
- 还向LanguageRegistry注册了一个默认的驱动类：XMLLanguageDriver，另外还注册了一个RawLanguageDriver

## 解析mappers节点/mapper文件解析

mapper文件解析工作交给XMLMapperBuilder来完成，XMLMapperBuilder对象初始化的时候会创建一个MapperBuilderAssistant对象。

XMLMapperBuilder解析mapper文件主要步骤有：

1. 解析mapper文件
2. 绑定mapper和namespace关系
3. 处理步骤1中解析失败的resultMap、cache-ref、sql等

### 解析mapper文件

- 处理namespace
- 解析cache-ref
- 解析cache
- 解析resultMap
- 解析sql
- 解析select、insert、update、delete

### 绑定mapper和namespace

mapper文件中有namespace属性，内容是Mappe接口的全限定名，也就是mapper和Mapper接口是一一对应关系，这里绑定，就是将namespace中指定的Mapper接口类加载，并将这个接口保存到MapperRegistry中的knownMappers这个map中去。这个map的key是Mapper接口，value是一个对一个的MapperProxyFactory。

以后使用一个Mapper的时候，就是从MapperRegistry中进行获取，MapperRegistry中缓存这接口对应的MapperProxyFactory，调用MapperProxyFactory的创建新实例方法就可以获得一个MapperProxy对象。MapperProxy是一个Mapper的代理对象，调用Mapper接口的时候实际上是把逻辑处理交给了MapperProxy，MapperProxy会进行继续的sql执行等等。

### 解析select、insert、update、delete

XMLMapperBuilder在解析select、insert、update、delete等结点的时候，将具体的解析工作交给了XMLStatementBuilder来进行，XMLStatementBuilder会把每个节点解析成一个MappedStatement对象。

XMLStatementBuilder会解析节点中的各个属性，然后使用上面初始化的的MapperBuilderAssistant对象将构建好的MappedStatement对象添加到Configuration对象的mappedStatements这个map中。

这样解析完mybatis-config.xml配置文件以及mapper文件后，MyBatis就启动完成了。简单总结下就是：

- 解析mybatis-config.xml配置文件
- 解析mapper文件
- 将Mapper接口和一个MapperProxyFactory绑定起来