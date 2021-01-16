---
title: DynamicConfigCenter设计文档
date: 2020-04-26 20:07:40
categories: 
- DynamicConfigCenter
tags:
- DynamicConfigCenter
---



大概列一下动态配置中心需要实现的功能。

<!--more-->

- DynamicConfigCenter-admin，配置等的管理
- DynamicConfigCenter-client，应用依赖的客户端包

源码：[https://github.com/dachengxi/DynamicConfigCenter](https://github.com/dachengxi/DynamicConfigCenter)

都是使用draw io画的图，源文件可以导入修改。

# 整体架构

架构图源文件：[DynamicConfigCenter架构.drawio](/DynamicConfigCenter设计文档/DynamicConfigCenter架构.drawio)

![DynamicConfigCenter架构](/DynamicConfigCenter设计文档/DynamicConfigCenter架构.png)

# 数据库设计

- dcc_group，配置组，可以以应用作为组
- dcc_env，环境，区分开发、测试等环境
- dcc_config，配置
- dcc_config_inst，配置实例，不同环境的配置值

draw io源文件：[DynamicConfigCenter数据库设计](/DynamicConfigCenter设计文档/DynamicConfigCenter数据库设计.drawio)

![DynamicConfigCenter数据库设计](/DynamicConfigCenter设计文档/DynamicConfigCenter数据库设计.png)

## dcc_group

| 名称          | 类型         | 是否为空 | 索引    | 默认值 | 备注     |
| ------------- | ------------ | -------- | ------- | ------ | -------- |
| id            | bigint(20)   | N        | PRIMARY |        | 主键ID   |
| created_time  | datetime     | N        |         |        | 创建时间 |
| modified_time | datetime     | N        |         |        | 修改时间 |
| version       | smallint(6)  | N        |         |        | 版本号   |
| name          | varchar(255) | N        |         |        | 组名     |
| desc          | varchar(255) | N        |         |        | 描述     |

## dcc_env

| 名称          | 类型         | 是否为空 | 索引    | 默认值 | 备注     |
| ------------- | ------------ | -------- | ------- | ------ | -------- |
| id            | bigint(20)   | N        | PRIMARY |        | 主键ID   |
| created_time  | datetime     | N        |         |        | 创建时间 |
| modified_time | datetime     | N        |         |        | 修改时间 |
| version       | smallint(6)  | N        |         |        | 版本号   |
| name          | varchar(255) | N        |         |        | env名    |
| desc          | varchar(255) | N        |         |        | 描述     |

## dcc_config

| 名称          | 类型         | 是否为空 | 索引    | 默认值 | 备注                          |
| ------------- | ------------ | -------- | ------- | ------ | ----------------------------- |
| id            | bigint(20)   | N        | PRIMARY |        | 主键ID                        |
| created_time  | datetime     | N        |         |        | 创建时间                      |
| modified_time | datetime     | N        |         |        | 修改时间                      |
| version       | smallint(6)  | N        |         |        | 版本号                        |
| key           | varchar(255) | N        |         |        | 配置key                       |
| type          | smallint(6)  | N        |         |        | 类型 1-String 2-Number 3-Json |
| desc          | varchar(255) | N        |         |        | 描述                          |
| group_id      | bigint(20)   | N        |         |        | 所属组id                      |

## dcc_config_inst

| 名称          | 类型        | 是否为空 | 索引    | 默认值 | 备注     |
| ------------- | ----------- | -------- | ------- | ------ | -------- |
| id            | bigint(20)  | N        | PRIMARY |        | 主键ID   |
| created_time  | datetime    | N        |         |        | 创建时间 |
| modified_time | datetime    | N        |         |        | 修改时间 |
| version       | smallint(6) | N        |         |        | 版本号   |
| config_id     | bigint(20)  | N        |         |        | 配置id   |
| env_id        | bigint(20)  | N        |         |        | 环境id   |
| value         | text        | N        |         |        | 配置值   |

# Zookeeper节点

数据节点：`/cxis/dcc/${group_name}/key`