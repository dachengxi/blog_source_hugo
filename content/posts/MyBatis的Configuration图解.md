---
title: MyBatis的Configuration图解
date: 2019-06-07 22:10:30
categories: MyBatis
tags: 
- MyBatis
---

MyBatis的配置文件mybatis-config.xml、映射配置文件mapper.xml以及Mapper接口中的注解信息等等，都会在解析后保存在Configuration对象中。Configuration对象是核心内容。

<!--more-->

以下是画的思维导图，这里是processon的源文件：[MyBatisConfiguration.pos](MyBatisConfiguration.pos)，可以自行导入。

![MyBatis的Configuration图解](MyBatisConfiguration.png)