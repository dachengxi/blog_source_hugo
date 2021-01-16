---
title: MyBatis整体架构设计
date: 2019-06-01 14:14:34
categories: MyBatis
tags: 
- MyBatis
---

学习一下MyBatis整体架构设计，以及主要模块的功能说明。

<!--more-->

MyBatis整体上分为三层：接口层、核心处理层、基础支持层：

![Mybatis整体架构设计](/MyBatis整体架构设计/mybatis-1.png)

图片源自《MyBatis技术内幕》

# 接口层

接口层是提供给应用程序使用的，也就是使用SqlSession来执行sql语句。

# 核心处理层

- 配置解析
- 参数映射
- SQL解析
- SQL执行
- 结果集映射
- 插件

## 配置解析

配置解析发生在MyBatis启动阶段，加载mybatis-config.xml配置文件以及我们自定义的mapper.xml文件或者是注解定义的Mapper接口文件，MyBatis解析这些文件后会生成对应的对象，保存在Configuration对象中。

## 参数映射

Java类型和JDBC类型之间的转换。

## SQL解析

MyBatis提供动态SQL功能，MyBatis会根据传入的实参，解析映射文件中定义的动态SQL节点并生成可执行的SQL，然后处理SQL中占位符绑定传入的实参。

## SQL执行

SQL执行涉及多个组件：

- Executor维护一级和二级缓存，提供事务管理相关操作，将数据库相关操作委托给StatementHandler完成。
- StatementHandler调用ParmeterHandler完成SQL实参绑定，然后通过Statement执行SQL得到结果。
- ResultSetHandler进行结果集映射，得到结果对象返回。

## 结果集映射

ResultSetHandler进行结果集映射，将从数据库中查询的结果映射成我们需要的Java对象。

## 插件

MyBatis提供插件接口，可以进行扩展，比如可以拦截SQL进行重写。

# 基础支持层

- 数据源模块
- 事务管理模块
- 缓存模块，提供了一级缓存和二级缓存
- Binding模块，将用户自定义的Mapper接口和映射配置文件关联起来
- 反射模块，对Java原生反射进行封装，优化，加缓存提高性能
- 类型转换，JDBC和Java类型之间的转换，SQL绑定实参和映射结果集都会使用
- 日志模块
- 资源加载，对类加载器进行封装
- 解析器模块，解析mybatis-config-xml配置文件以及映射文件，为处理动态SQL语句中占位符提供支持



# 参考

- 《MyBatis技术内幕》