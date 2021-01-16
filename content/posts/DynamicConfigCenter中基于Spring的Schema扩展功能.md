---
title: DynamicConfigCenter中基于Spring的Schema扩展功能
date: 2020-05-04 10:22:12
categories: 
- DynamicConfigCenter
tags:
- DynamicConfigCenter
---

集成Spring，使用Spring的scheme扩展机制。

<!--more-->

# Spring的Schema扩展机制介绍

可以自定义xml标签，将我们自定义的功能集成到Spring中去，大概步骤如下：

- classpath下的META-INF中定义文件：spring.handlers、spring.schemas，用来识别配置
- 编写xml的xsd文件描述自定义元素
- 编写NamespaceHandler实现类，来注册自定义标签的解析器
- 编写BeanDefinitionParser实现类，具体解析标签

# DynamicConfigCenter中对Schema扩展机制的使用和实现

## 实际使用

- 在xml配置文件中定义`<dcc:config .../>`用来启用自己写的解析placeholder的方法
- 在代码中使用`@Value("${TestProject.user.prefix}")`注入配置
- 如果本地配置文件有要使用的配置，优先使用本地配置文件中的配置

## 具体实现

定义spring.schemas文件：

```properties
https\://www.cxis.me/schema/dcc/dcc-config-1.0.xsd=me/cxis/dcc/spring/config/dcc-config.xsd
```

定义spring.handlers文件：

```properties
https\://www.cxis.me/schema/dcc=me.cxis.dcc.spring.DccConfigNamespaceHandler
```

定义dcc-config.xsd文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<xsd:schema xmlns="https://www.cxis.me/schema/dcc"
		xmlns:xsd="http://www.w3.org/2001/XMLSchema"
		xmlns:beans="http://www.springframework.org/schema/beans"
		targetNamespace="https://www.cxis.me/schema/dcc"
		elementFormDefault="qualified"
		attributeFormDefault="unqualified">

	<xsd:import namespace="http://www.springframework.org/schema/beans" schemaLocation="https://www.springframework.org/schema/beans/spring-beans.xsd"/>

	<xsd:element name="config">
		<xsd:complexType>
			<xsd:complexContent>
				<xsd:extension base="beans:identifiedType">
				</xsd:extension>
			</xsd:complexContent>
		</xsd:complexType>
	</xsd:element>

</xsd:schema>

```

定义DccConfigNamespaceHandler：

```java
public class DccConfigNamespaceHandler extends NamespaceHandlerSupport {

    @Override
    public void init() {
        registerBeanDefinitionParser("config", new DccConfigDefinitionParser());
    }
}
```

定义DccConfigDefinitionParser：

```java
public class DccConfigDefinitionParser implements BeanDefinitionParser {

    @Override
    public BeanDefinition parse(Element element, ParserContext parserContext) {
        return null;
    }
}
```

运行时监听zookeeper的变更，使用反射更新@Value注解的字段值。

具体代码参照https://github.com/dachengxi/DynamicConfigCenter中DynamicConfigCenter-client下的`me.cxis.dcc.spring.DccConfigPlaceholderConfigurer`。



源码：[https://github.com/dachengxi/DynamicConfigCenter](https://github.com/dachengxi/DynamicConfigCenter)

