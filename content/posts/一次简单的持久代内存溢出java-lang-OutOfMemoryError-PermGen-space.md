---
title: '一次简单的持久带内存溢出java.lang.OutOfMemoryError: PermGen space'
date: 2016-06-28 13:51:13
categories: 遇到的问题
tags:
- java.lang.OutOfMemoryError:-PermGen-space
- 持久带内存溢出
---

## 简介

昨天拿到服务器之后,便部署新开发的项目上去.顺带测试,运行不久,发现程序运行缓慢,随即提示java.lang.OutOfMemoryError: PermGen space.以前没遇到过这种情况,记录下来.
服务器上的软件以及设置是一个酱油弄的,没仔细去查看,不知道是不是有问题.
<!-- more -->
## 查看tomcat pid

```
jps -l 
发现两个tomcat
jps -v
找到我部署程序的那个tomcat pid 32365
```
## 查看虚拟机相关信息

### 查看jstat -gc

```
jstat -gc 32365
S0C    S1C    S0U    S1U      EC       EU        OC         OU       PC     PU    YGC     YGCT    FGC    FGCT     GCT
6848.0 6848.0 0.0    0.0   54976.0   742.8    137236.0   75708.3   65536.0 65535.9    115    1.345  688   172.945  174.290
```
发现PC和PU两个数值相同了,持久代已经使用完.

参数代表含义如下:

```
S0C 年轻代中第一个survivor（幸存区）的容量 (字节)
S1C 年轻代中第二个survivor（幸存区）的容量 (字节)
S0U 年轻代中第一个survivor（幸存区）目前已使用空间 (字节)
S1U 年轻代中第二个survivor（幸存区）目前已使用空间 (字节)
EC 年轻代中Eden（伊甸园）的容量 (字节)
EU 年轻代中Eden（伊甸园）目前已使用空间 (字节)
OC Old代的容量 (字节)
OU Old代目前已使用空间 (字节)
PC Perm(持久代)的容量 (字节)
PU Perm(持久代)目前已使用空间 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
YGCT 从应用程序启动到采样时年轻代中gc所用时间(s)
FGC 从应用程序启动到采样时old代(全gc)gc次数
FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT 从应用程序启动到采样时gc用的总时间(s)
```

### 查看jstat -gcpermcapacity

```
jstat -gcpermcapacity 32365
PGCMN      PGCMX       PGC         PC      YGC   FGC    FGCT     GCT
12288.0    65536.0    65536.0    65536.0   115   610  153.078  154.423
```
持久代初始大小(PGCMN)12M,最大(PGCMX)64M,上面一步显示,64M已经用完.可以考虑增加持久代大小64M增大到256M.

参数代表含义如下:

```
PGCMN perm代中初始化(最小)的大小 (字节)
PGCMX perm代的最大容量 (字节)
PGC perm代当前新生成的容量 (字节)
PC Perm(持久代)的容量 (字节)
YGC 从应用程序启动到采样时年轻代中gc次数
FGC 从应用程序启动到采样时old代(全gc)gc次数
FGCT 从应用程序启动到采样时old代(全gc)gc所用时间(s)
GCT 从应用程序启动到采样时gc用的总时间(s)
```
### 设置tomcat
1. tomcat bin 目录下找到setenv.sh,没有的话新建一个
2. 添加如下内容到setenv.sh `export CATALINA_OPTS="$CATALINA_OPTS -XX:PermSize=256m -XX:MaxPermSize=256m"`
3. 重启tomcat

## 参考
[http://stackoverflow.com/questions/19769675/tomcat-7-outofmemoryerror-from-uncaughtexceptionhandler](http://stackoverflow.com/questions/19769675/tomcat-7-outofmemoryerror-from-uncaughtexceptionhandler)

[http://tdcq.iteye.com/blog/1990666](http://tdcq.iteye.com/blog/1990666)