---
title: Spring同一个类中的注解方法调用AOP失效问题总结
date: 2020-03-23 23:01:20
categories: Spring
tags:
- Spring
---

Spring中在同一个类中两个方法调用，会导致代理失效，比如同一个类中一个方法A调用另外一个带事务的方法B，会发现B方法的事务不生效；同一个类中一个方法A调用另外一个带注解的清除缓存的方法B，会发现清除缓存不成功。

<!--more-->

> Spring中仅支持方法级别的代理。

Spring中代理是动态代理，也就是在运行时生成代理，代理可以大概使用以下代码来描述下：

```java
@Service
public class A {
    
    public void a() {
        ...;
        b();
        ...;
    }
    
    @Transactional
    public void b() {
        ...;   
    }
}
```

而在运行时生成的代理大概如下：

```java
public class A$Proxy {
    A serviceA = new A();
    
    public void a() {
        serviceA.a();
    }
    
    public void b() {
        transaction start();
        serviceA.b();
        transaction end();
    }
}
```

如果我们调用A这个Bean其实调用的是A$Proxy，对于a方法的调用其实是调用的A中的a方法，而A中的a调用的是A中的b方法，并没有调用A$Proxy中的b方法来实施事务。

解决办法：

1. 将方法写到两个类中去。
2. 开启expose-proxy

开启expose-proxy后，我们代码需要类似如下处理：

```java
@Service
public class A {
    
    public void a() {
        ...;
        ((A)AopContext.currentProxy()).b();
        ...;
    }
    
    @Transactional
    public void b() {
        ...;   
    }
}
```

这种方法在Spring中的实现大概是：Spring在代理使用的时候，会将当前代理设置到AopContext中去：`AopContext.setCurrentProxy(proxy)`，所以在我们上面修改后的代码里`AopContext.currentProxy()`就可以拿到A$Proxy，再调用b方法的时候，就是调用的A$Proxy中的增强过的b方法了。