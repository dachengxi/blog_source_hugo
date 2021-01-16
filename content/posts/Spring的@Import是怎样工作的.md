---
title: Spring的@Import是怎样工作的
date: 2019-05-15 21:04:35
categories: Spring
tags:
- Spring
---

Spring提供了JavaConfig配置的方式，可以将原来的xml配置文件使用JavaConfig配置的方式来替换。xml配置文件可以类比@Configuration注解的文件，@Import注解可以类比xml配置文件中的`<import>`标签，@Bean注解可以类比xml配置文件的`<bean>`标签。`<import>`标签用来将多个分散的xml配置文件整合起来，@Import注解可以将多个分散的@Configuration整合起来。

<!--more-->

另外@Import也可以导入一个普通的类。@Import只允许放到类上，不能放在方法上。

@Import注解有四种使用方法：

1. @Import导入普通类（Spring4.2之后支持）
2. @Import导入@Configuration注解的类
3. @Import导入ImportBeanDefinitionRegistrar的实现类
4. @Import导入ImportSelector的实现类

# 导入普通类

导入普通类的意思就是，我们可以写一个类，不需要任何注解，比如@Service之类的。我们可以在我们的配置类里把我们的这个普通类导入进来`@Import({Xxxxx.class})`，这样就可以在配置类处理的时候把我们的普通类注册成一个Bean。

# 导入@Configuration注解的类

假设一个类中使用@Configuration注解，这个类中有很多的@Bean注解方法，我们可以在我们的配置类中使用@Import将这个类导入。

# 导入ImportBeanDefinitionRegistrar实现类

我们可以定义一个普通类，实现接口ImportBeanDefinitionRegistrar，并实现其方法registerBeanDefinitions，之后就可以在我们的配置类中中使用`@Import({Xxxxx.class})`将我们的自定义类导入，这样我们会把registerBeanDefinitions方法中指定的类注册到容器中，但是这个自定义类本身不会被加载进去。

# 导入ImportSelector实现类

们可以定义一个普通类，实现接口ImportSelector，并实现其方法selectImports，之后我们就可以在配置类中使用`@Import({Xxxx.class})`将自定义类导入，这样我们会把selectImports方法中返回的所有的类注册到容器中。

# @Import的实现原理

Spring容器初始化的时候有一步invokeBeanFactoryPostProcessor，这里会调用实现了接口BeanDefinitionRegistryPostProcessor的postProcessBeanDefinitionRegistry方法，ConfigurationClassPostProcessor也实现了此接口，在该方法postProcessBeanDefinitionRegistry中会处理@Configuration相关注解，这里就进行了@Import注解的处理。

ConfigurationClassParser的processImport方法：

```java
...
    // ImportSelector的处理，会实例化我们自定义的实现类ImportSelector接口的类 ，然后调用ImportSelector的selectImports方法获取我们要处理Bean
if (checkAssignability(ImportSelector.class, candidateToCheck)) {
    // Candidate class is an ImportSelector -> delegate to it to determine imports
    Class<?> candidateClass = (candidate instanceof Class ? (Class) candidate :
                               this.resourceLoader.getClassLoader().loadClass((String) candidate));
    ImportSelector selector = BeanUtils.instantiateClass(candidateClass, ImportSelector.class);
    processImport(configClass, metadata, Arrays.asList(selector.selectImports(metadata)), false);
}
// ImportBeanDefinitionRegistrar的处理，实例化我们自定义的实现了ImportBeanDefinitionRegistrar接口的类，然后调用registerBeanDefinitions方法将我们的Bean注册到容器中
else if (checkAssignability(ImportBeanDefinitionRegistrar.class, candidateToCheck)) {
    // Candidate class is an ImportBeanDefinitionRegistrar ->
    // delegate to it to register additional bean definitions
    Class<?> candidateClass = (candidate instanceof Class ? (Class) candidate :
                               this.resourceLoader.getClassLoader().loadClass((String) candidate));
    ImportBeanDefinitionRegistrar registrar =
        BeanUtils.instantiateClass(candidateClass, ImportBeanDefinitionRegistrar.class);
    invokeAwareMethods(registrar);
    registrar.registerBeanDefinitions(metadata, this.registry);
}
else {
    // 除了上面两种情形之外的@Import注解，其他的当做配置类来处理
    // Candidate class not an ImportSelector or ImportBeanDefinitionRegistrar ->
    // process it as a @Configuration class
    this.importStack.registerImport(metadata,
                                    (candidate instanceof Class ? ((Class) candidate).getName() : (String) candidate));
    processConfigurationClass(candidateToCheck instanceof Class ?
                              new ConfigurationClass((Class) candidateToCheck, true) :
                              new ConfigurationClass((MetadataReader) candidateToCheck, true));
}
...
```

# ImportSelector的实现类简介

基于Spring 3.2.18.RELEASE源码版本

## AsyncConfigurationSelector

AsyncConfigurationSelector在@EnableAsync中被导入，根据@EnableAsync注解的mode属性来决定要导入哪个AbstractAsyncConfiguration的具体实现类。

## CachingConfigurationSelector

根据@EnableCaching的mode属性来决定要导入哪个AbstractCachingConfiguration的具体实现类

## TransactionManagementConfigurationSelector

根据@EnableTransactionManagement注解的mode属性来确定要导入哪个AbstractTransactionManagementConfiguration的具体实现类

# ImportBeanDefinitionRegistrar的实现类简介

基于Spring 3.2.18.RELEASE源码版本

## AspectJAutoProxyRegistrar

在@EnableAspectJAutoProxy注解中使用，用来注册AnnotationAwareAspectJAutoProxyCreator到容器中，如果指定使用proxyTargetClass，则强制使用proxyTargetClass。

## AutoProxyRegistrar

注册自动代理创建器到容器中