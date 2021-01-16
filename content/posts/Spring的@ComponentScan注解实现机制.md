---
title: Spring的@ComponentScan注解实现机制
date: 2019-08-03 22:34:33
categories: Spring
tags:
- Spring
---

记录下Spring中@ComponentScan注解的实现原理。

<!--more-->

@ComponentScan和@Configuration注解配合使用，来扫描指定位置下的组件到容器中。使用xml配置的时候，会使用`<context:component-scan>`来启用对组件的扫描功能。

使用xml配置方式的时候，容器发现了`<context:component-scan>`标签后，会使用ContextNamespaceHandler中注册的ComponentScanBeanDefinitionParser来对指定位置进行组件扫描，将扫描到的组件注册到容器中。扫描的组件包括@Component、@Controller、@Service、@Repository等。

使用@ComponentScan的时候，需要和@Configuration配合使用，在解析@Configuration注解的时候会使用ConfigurationClassParser来解析，在解析的时候就会有一步是解析@ComponentScan注解的，发现了@ComponentScan注解的时候，会使用ComponentScanAnnotationParser进行解析，这里就和ComponentScanBeanDefinitionParser一样了，都是使用ClassPathBeanDefinitionScanner来进行组件的扫描，将组件都扫描进容器中。

ClassPathBeanDefinitionScanner用来在classpath上扫描组件并解析成BeanDefinition注册到容器中，扫描的注解包括：@Component、@Repository、@Controller、@ManagedBean、@Named，或者使用自定义的type filters。