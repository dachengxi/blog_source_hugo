---
title: DynamicConfigCenter中的监听器模式
date: 2020-04-28 14:17:16
categories: 
- DynamicConfigCenter
tags:
- DynamicConfigCenter
---

监听器模式运用。

<!--more-->

# 观察者模式

观察者模式，定义了一对多的依赖关系，多个观察者对象同时监听某一个主题对象，主题对象发生变化时，会通知所有观察者。

- Subject，抽象主题，定义添加和移除观察中对象的方法、定义保存观察者对象的容器、定义通知观察者对象的方法
- ConcreteSubject，实际主题，对抽象主题的实现
- Observer，观察者对象抽象，定义被通知的接口
- ConcreteObserver，观察者实现

代码示例：

```java
public abstract class Subject {

    protected List<Observer> observers = new ArrayList<>();

    public void addObserver(Observer observer) {
        observers.add(observer);
    }

    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    public void notifyObservers() {
        observers.forEach(Observer::receiveUpdateFromSubject);
    }
}
```

```java
public class ConcreteSubject extends Subject {

    public void update() {
        this.notifyObservers();
    }
}
```

```java
public interface Observer {

    void receiveUpdateFromSubject();
}
```

```java
public class ConcreteObserver implements Observer {

    @Override
    public void receiveUpdateFromSubject() {
        System.out.println("receive update form subject......");
    }
}
```

```java
public class Client {

    public static void main(String[] args) {
        ConcreteSubject concreteSubject = new ConcreteSubject();
        Observer concreteObserver = new ConcreteObserver();
        concreteSubject.addObserver(concreteObserver);;
        
        concreteSubject.update();
    }
}
```

# 监听器模式

监听器模式是观察者模式的一种实现。

- 事件源，事件发生的源头、触发事件的地方，事件源是被监听的对象
- 事件，事件源产生的事件
- 监听器，监听事件的发生，属于观察者

代码示例：

```java
public class Event {

    private String key;

    private String value;

    private String eventType;

    public String getKey() {
        return key;
    }

    public void setKey(String key) {
        this.key = key;
    }

    public String getValue() {
        return value;
    }

    public void setValue(String value) {
        this.value = value;
    }

    public String getEventType() {
        return eventType;
    }

    public void setEventType(String eventType) {
        this.eventType = eventType;
    }

    @Override
    public String toString() {
        return "Event{" +
            "key='" + key + '\'' +
            ", value='" + value + '\'' +
            ", eventType='" + eventType + '\'' +
            '}';
    }
}
```

```java
public interface EventListener {

    public void receiveEventFromEventSource(Event event);
}
```

```java
public class ConcreteEventListener implements EventListener {

    @Override
    public void receiveEventFromEventSource(Event event) {
        System.out.println("receive event from event source: " + event);
    }
}
```

```java
public abstract class EventSource {

    protected List<EventListener> eventListeners = new ArrayList<>();

    public void addEventListener(EventListener eventListener) {
        eventListeners.add(eventListener);
    }

    public void removeEventListener(EventListener eventListener) {
        eventListeners.remove(eventListener);
    }

    public void notifyListeners(Event event) {
        eventListeners.forEach(eventListener -> eventListener.receiveEventFromEventSource(event));
    }
}
```

```java
public class ConcreteEventSource extends EventSource {

    public void eventHappened() {
        Event event = new Event();
        event.setKey("user.prefix");
        event.setValue("UserDev_");
        event.setEventType("Type_DateChanged");
        notifyListeners(event);
    }
}

```

```java
public class Client {

    public static void main(String[] args) {
        ConcreteEventSource concreteEventSource = new ConcreteEventSource();
        EventListener eventListener = new ConcreteEventListener();
        concreteEventSource.addEventListener(eventListener);

        concreteEventSource.eventHappened();
    }
}
```

# DynamicConfigCenter中的应用

- 使用Zookeeper的Watcher机制，发生变更的时候主动更新DynamicConfigCenter-client本地缓存
- 自定义ConfigListener，项目使用的时候可以自定义对某个key设置监听器，Zookeeper通知变更的时候，会回调用户自定的监听器进行通知。

源码：[https://github.com/dachengxi/DynamicConfigCenter](https://github.com/dachengxi/DynamicConfigCenter)