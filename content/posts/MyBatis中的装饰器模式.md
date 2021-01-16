---
title: MyBatis中的装饰器模式
date: 2019-07-07 20:48:19
categories: MyBatis
tags: 
- MyBatis
---

在MyBatis的Executor和缓存的学习的时候，提到了CachingExecutor使用了装饰器模式来增加二级缓存，这里对MyBatis中的装饰器模式进行简单学习。

<!--more-->

装饰器模式（Decorator Pattern）可以向一个现有的对象增加一些额外的功能，而又不改变其结构。MyBatis中CachingExecutor就是使用了装饰器模式来给普通的Executor增加了缓存功能，也就是二级缓存。

装饰器模式中的角色：

- 抽象组件（Component），一般就是一个接口，定义了一些规范。
- 具体组件实现（ConcreteComponent），接口具体的实现类。
- 装饰器（Decorator），实现抽象组件接口，并且内部持有一个Component实现。
- 具体装饰器（ConcreteDecorator），向组件添加新的功能

# CachingExecutor

MyBatis中CachingExecutor的实现与标准的装饰器模式有一点点的不同：

- 抽象组件：Executor
- 具体组件实现：SimpleExecutor、ReuseExecutor、BatchExecutor
- 装饰器：CachingExecutor
- 具体装饰器：CachingExecutor

简要代码如下：

```java
public interface Executor {
  // ...
}
```

```java
public class SimpleExecutor extends BaseExecutor {
  // ...
}
```

```java
public class CachingExecutor implements Executor {

  private Executor delegate;
  // ...
  
  public CachingExecutor(Executor delegate) {
    this.delegate = delegate;
    // ...
  }
  
  public <E> List<E> query(/*.....*/) throws SQLException {
    // ... 查询缓存
    delegate.query();
    // ... 写缓存
  }
  
  // ...
  
}
```

# Cache

MyBatis中的一级缓存也是使用了装饰器模式：

- 抽象组件：Cache
- 具体组件实现：PerpetualCache
- 装饰器：FifoCache、LruCache、WeakCache、BlockingCache等等
- 具体装饰器：FifoCache、LruCache、WeakCache、BlockingCache等等

简要代码如下：

```java
public interface Cache {
  // ...
}
```

```java
public class PerpetualCache implements Cache {
  // ...
}
```

```java
public class LruCache implements Cache {

  private final Cache delegate;
  
  public LruCache(Cache delegate) {
    this.delegate = delegate;
  }
  
  // ...
  @Override
  public void putObject(Object key, Object value) {
    delegate.putObject(key, value);
    // 基于LinkedHashMap实现了LRU
    cycleKeyList(key);
  }
}
```

