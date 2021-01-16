---
title: Spring中使用Groovy的示例
date: 2020-08-25 22:17:07
categories: Spring
tags:
- Spring

---

Spring对动态语言提供了支持，最近准备在项目中使用Groovy实现功能的热插拔，这里记录下Spring中使用Groovy的示例。

<!--more-->

先定义一个接口，用来处理消息的接口：

```java
package me.cxis.spring.groovy;

/**
 * 处理器接口，提供给groovy脚本来实现
 */
public interface Processor {

    /**
     * 处理消息，不同的处理方式，可使用不同的groovy脚本实现
     * @param message
     * @return
     */
    String process(String message);
}
```

接口的实现都是使用Groovy脚本，这样就可以利用Groovy脚本来动态实现功能替换了。

这里通过Groovy脚本文件、xml中内嵌脚本、从数据库中获取脚本等三种方式来说明。

# 使用Groovy脚本文件

定义消息处理的具体实现，ClasspathMessageProcessor.groovy：

```groovy
import me.cxis.spring.groovy.Processor

class ClasspathMessageProcessor implements Processor {

    @Override
    String process(String message) {
        println('classpath 的groovy文件处理消息：'+ message)

        def  result = message;
        if (message.contains("error")) {
            result = '消息里有错误信息'
        }
        result = result + "，经过了classpath的groovy文件处理"
        return result
    }
}
```

接下来定义spring的配置文件，目前还不支持使用注解或者Java Config的方式来配置动态语言，只能使用xml的配置方式，spring-groovy.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:lang="http://www.springframework.org/schema/lang"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/lang
                           http://www.springframework.org/schema/lang/spring-lang.xsd">

    <!--<lang:defaults refresh-check-delay="1000"/>-->

    <lang:groovy id = "processorFromClasspathFile" script-source = "classpath:groovy/ClasspathMessageProcessor.groovy"/>

</beans>
```

xml配置文件中指定了id和groovy文件的位置，可以理解成是一个Bean，接下来就是代码中使用，可以像普通的Bean进行注入：

```java
package me.cxis.spring.groovy;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;

/**
 * 消息处理
 */
@Component
public class MessageProcessManager {

    private static final Logger LOGGER = LoggerFactory.getLogger(MessageProcessManager.class);

    @Resource
    private Processor processorFromClasspathFile;

    /**
     * 使用classpath中的groovy脚本
     * @param message
     * @return
     */
    public String processMessageFromClasspathFile(String message) {
        LOGGER.info("origin message: {}", message);
        String result = processorFromClasspathFile.process(message);
        LOGGER.info("process result: {}", result);
        return result;
    }
}

```

这样就可以使用了。

# xml中内嵌脚本

这种方式和上面的方式差不多，区别就是把Groovy脚本内容直接内嵌到了xml配置文件中，配置文件如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:lang="http://www.springframework.org/schema/lang"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/lang
                           http://www.springframework.org/schema/lang/spring-lang.xsd">

    <!--<lang:defaults refresh-check-delay="1000"/>-->

    <lang:groovy id = "processorFromInline">
        <lang:inline-script>
            import me.cxis.spring.groovy.Processor

            class ClasspathMessageProcessor implements Processor {

                @Override
                String process(String message) {
                    println('inline处理消息：'+ message)

                    def  result = message;
                    if (message.contains("error")) {
                        result = '消息里有错误信息'
                    }
                    result = result + "，经过了inline处理"
                    return result
                }
            }
        </lang:inline-script>
    </lang:groovy>
</beans>
```

接下来的使用方式和上面的使用方式一样，可以直接当成一个Bean注入到代码中。

# 从数据库中获取脚本

这种方式需要自定义获取脚本方式，下面是具体的实现步骤。

## 自定义ScriptSource

```java
package me.cxis.spring.groovy;

import com.google.common.io.Resources;
import org.springframework.scripting.ScriptSource;
import org.springframework.util.StringUtils;

import java.io.IOException;
import java.net.URL;
import java.nio.charset.StandardCharsets;

/**
 * 从数据库中读取groovy脚本内容
 */
public class DatabaseScriptSource implements ScriptSource {

    private final String scriptName;

    public DatabaseScriptSource(String scriptName) {
        this.scriptName = scriptName;
    }

    @Override
    public String getScriptAsString() throws IOException {
        // TODO 假装从数据库中根据scriptName读取
        System.out.println("xxxxxxxx:" + scriptName);
        URL url = this.getClass().getResource("/groovy/" + scriptName);
        String groovyContent = Resources.toString(url, StandardCharsets.UTF_8);
        System.out.println(groovyContent);
        return groovyContent;
    }

    @Override
    public boolean isModified() {
        return false;
    }

    @Override
    public String suggestedClassName() {
        return StringUtils.stripFilenameExtension(this.scriptName);
    }
}

```

这里就是自定义脚本获取的逻辑，是从数据库中根据脚本名字获取脚本内容，这里假装从数据库中获取。想要真从数据库中获取，需要自己实现。

## 重写ScriptFactoryPostProcessor的获取脚本来源的方法

```java
package me.cxis.spring.groovy;

import org.springframework.core.io.ResourceLoader;
import org.springframework.scripting.ScriptSource;
import org.springframework.scripting.support.ResourceScriptSource;
import org.springframework.scripting.support.ScriptFactoryPostProcessor;
import org.springframework.scripting.support.StaticScriptSource;
import org.springframework.stereotype.Component;

/**
 * 自定义ScriptFactoryPostProcessor，支持从数据中读取groovy脚本
 */
@Component
public class CustomScriptFactoryPostProcessor extends ScriptFactoryPostProcessor {

    public static final String LANG_PREFIX_DATABASE = "database:";

    @Override
    protected ScriptSource convertToScriptSource(String beanName, String scriptSourceLocator, ResourceLoader resourceLoader) {
        if (scriptSourceLocator.startsWith(INLINE_SCRIPT_PREFIX)) {
            return new StaticScriptSource(scriptSourceLocator.substring(INLINE_SCRIPT_PREFIX.length()), beanName);
        }
        else if (scriptSourceLocator.startsWith(LANG_PREFIX_DATABASE)) {
            return new DatabaseScriptSource(scriptSourceLocator.substring(LANG_PREFIX_DATABASE.length()));
        }
        else {
            return new ResourceScriptSource(resourceLoader.getResource(scriptSourceLocator));
        }
    }
}

```

这里其实就是根据xml配置文件中配置的脚本路径来使用不同加载器来加载脚本内容，我们定义了一个新的方式database，这个会在下面的xml中使用到。

## 定义xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:lang="http://www.springframework.org/schema/lang"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/lang
                           http://www.springframework.org/schema/lang/spring-lang.xsd">

    <!--<lang:defaults refresh-check-delay="1000"/>-->

    <!--这里指定了一个脚本名称，实际使用的时候可以动态生成此xml文件-->
    <lang:groovy id = "processorFromDB" script-source = "database:DBMessageProcessor.groovy"/>

</beans>
```

这里就是用到了我们定义的`database:`，后面紧跟的是Groovy脚本名称，可以根据这个名称从数据库中获取具体内容。当然这里是写死在配置文件中的一个Groovy，实际使用的时候可根据情况使用代码生成这个xml，然后加载进Spring容器中。举个例子，大概步骤可以如下：

- 数据库中有若干条名字不同的Groovy脚本数据
- 项目启动的时候加载这些数据库中的Groovy数据的名称
- 根据这些Groovy数据中的脚本名称，使用代码在内存中生成上面的xml文件
- 使用XmlBeanDefinitionReader来加载这个内存中的xml文件资源，这样就会按照上面我们自定义的方式根据脚本名字从数据库中加载脚本数据，并变成了Spring容器中的Bean
- 具体业务逻辑中就可以根据不同的名字从Spring容器中获取对应的Bean来使用
- Groovy有增删改的时候，可以使用消息等机制来通知应用有变动，应用根据实际情况来销毁和刷新容器的中Bean，这样可以实现了热插拔功能

这种方式稍微有点复杂，需要根据实际的情况来使用。