---
title: Spring中Placeholder的解析过程
date: 2020-05-05 10:46:26
categories: Spring
tags:
- Spring
---

Spring中Placeholder的解析过程简单梳理。

<!--more-->

Spring版本是3.2.18.RELEASE

# 使用

定义application.properties文件：

```properties
placeholder.user.prefix=UserPrefix_
```

在applicationContext.xml中引入application.properties文件：

```xml
<context:property-placeholder location="application.properties"/>
```

在实际使用的地方注入，这里使用@Value注解：

```java
@Value("${placeholder.user.prefix}")
private String userPrefix;
```

# 解析

1. 容器启动，读取xml配置文件，开始加载并解析成BeanDefinition
2. 在解析成BeanDefinition的时候读取到`<context:property-placeholder`，发现是自定义标签，使用ContextNamespaceHandler来处理，注册一个针对`property-placeholder`的处理器：PropertyPlaceholderBeanDefinitionParser
3. PropertyPlaceholderBeanDefinitionParser解析`<context:property-placeholder location="application.properties"/>`，转换为BeanDefinition，这个BeanDefinition里面持有了location，并且这个BeanDefinition对应的BeanClass是PropertyPlaceholderConfigurer
4. PropertyPlaceholderConfigurer查看继承关系，会发现它实现了BeanFactoryPostProcessor接口，在容器启动的invokeBeanFactoryPostProcessors这一步，会调用BeanFactoryPostProcessor的postProcessBeanFactory方法，该方法PropertyResourceConfigurer中有个实现
5. PropertyResourceConfigurer的postProcessBeanFactory方法中会把application.properties文件中的属性解析出来：`key=placeholder.user.prefix, value=UserPrefix_`
6. 接下来会继续调用PropertyPlaceholderConfigurer的processProperties方法，会遍历BeanFactory中的每个BeanDefinition，尝试使用从properties中解析到的key和value来替换BeanDefinition中对应的属性的`${}`占位符。这一步不是处理我们声明的@Value注解位置的占位符。
7. 在processProperties解析的最后会添加一个EmbeddedValueResolver到BeanFactory中，具体类型是：PlaceholderResolvingStringValueResolver，这个在Spring3.0新增的功能，用来解析embedded values的占位符，比如：注解的属性。
8. 接下来在容器启动的finishBeanFactoryInitialization这一步，会初始化所有的非懒加载的单例Bean，在populateBean这一步填充属性，在应用属性值之前，会调用实现了InstantiationAwareBeanPostProcessor接口的实现类的postProcessPropertyValues方法，这一步可以在应用属性值到Bean之前改变属性，比如可以替换我们的@Value中的占位符为真正的值
9. AutowiredAnnotationBeanPostProcessor实现了InstantiationAwareBeanPostProcessor接口，用来处理@Autowired、@Value、@Inject注解，这里的postProcessPropertyValues方法就用来处理我们@Value注解
10. 获取到注解元数据`@Value("${placeholder.user.prefix}")`以及要注入的属性`private String userPrefix;`，并且使用上面获得的PlaceholderResolvingStringValueResolver来替换占位符。
11. 替换占位符的过程就是将前缀`${`和后缀`}`截取掉，获得真正的`placeholder.user.prefix`，这样就可以根据这个key在上面解析的属性中找到对应的值了。
12. 解析出来key：`placeholder.user.prefix`之后，会调用PropertyPlaceholderConfigurer的resolvePlaceholder方法从上面解析的属性中根据key获取真正的值，这里会返回UserPrefix_
13. 接下来还有对表达式的解析，我们没使用，直接跳过。
14. 拿到真正的值`UserPrefix_`后，使用反射将值赋值给`private String userPrefix`，这时候就完成了。

# 总结

- 在解析xml成BeanDefinition的时候拿到application.properties文件的位置
- 在调用BeanFactoryPostProcessor实现类的postProcessBeanFactory方法的时候会把application.properties文件中的内容解析key和value对应关系
- 在实例化Bean的时候会调用AutowiredAnnotationBeanPostProcessor来处理@Value注解，将占位符替换为上面解析到的key对应的value，使用反射设置值。

