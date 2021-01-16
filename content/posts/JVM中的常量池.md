---
title: JVM中的常量池
date: 2017-07-08 11:42:10
categories: 
- JVM
tags:
- JVM
---

JVM中的常量池有：运行时常量池、Class文件常量池、字符串常量池、包装类常量池。

<!--more-->

可以先看下JVM运行时数据区域图：

![JVM内部结构](/JVM中的常量池/JVM_Internal_Architecture.png)

图片来自：https://blog.jamesdbloom.com/JVMInternals.html

在Non Heap非堆中包含了Code Cache和永久代，永久代中包含Interned String和方法区，方法区中包含了运行时常量池。

# Class文件常量池

编译后的class文件中，除了包含类的版本、字段、方法、接口等的描述信息外，还包含常量池表（Constant Pool Table），用来存放编译器生成的字面量（Literal）和符号引用（Symbolic References）。

## 字面量

字面量比较接近于Java语言层面的常量概念，比如：

- 文本字符串，`private String s = "xxx";`中的`"xxx"`是字面量
- 被声明为final的常量值，包括静态变量、实例变量、局部变量的值。`private final static int i = 3;`中的3是字面量

## 符号引用

符号引用属于编译原理方面的概念，包括：

- 被模块导出或者开放的包（Package）
- 类和接口的全限定名（Fully Qualified Name）
- 字段的名称和描述符（Descriptor）
- 方法的名称和描述符
- 方法句柄和方法类型（Method Handle、Method Type、Invoke Dynamic）
- 动态调用点和动态常量（Dynamically-Computed Call Site、Dynamically-Computed Constant）

Java代码在编译的时候，没有连接这一步，而是在JVM加载Class文件的时候在解析这一步进行动态连接，将符号引用替换为直接引用。也就是说Class文件中不会保存方法、字段等在内存中的布局信息。而是在类加载的解析这一步将符号引用翻译为具体的内存地址。

# 运行时常量池

Class文件中存在一个Class文件常量池，编译期生成的字面量和符号引用存在Class文件常量池中。Class文件常量池中的内容在类加载的时候会放到方法区的运行时常量池中。

运行时常量池（Runtime Constant Pool）是方法区的一部分。

JVM类加载过程的加载阶段，会将class文件中的静态结构转化为方法区的运行时数据结构，这里就包含了Class文件常量池加载进运行时常量池中。在解析阶段会把会把符号引用替换为直接引用。

运行时常量池具有动态性，运行时常量池中内容不全部来自Class文件常量池，在运行时可以通过代码生成常量并将其放入运行时常量池中，比如：`String.intern()`。

# 字符串常量池

- 为了避免多次创建字符串对象，JVM开辟出一块字符串常量池空间用来存储不重复的字符串。
- 直接使用双引号`""`声明的字符串，比如`String s = "abcd";`，JVM会先去常量池中查找有没有相同的字符串常量，如果有就把常量池的引用返回给变量；如果没有就会在字符串常量池中创建一个字符串常量，然后把这个引用返回给变量。
- 使用new关键字创建String对象，比如`String s = new String("abcd");`，如果字符串常量池中不存在这个字符串常量，会创建两个对象，一个是在字符串常量池中的字符串常量：abcd，一个是在堆中的String对象，返回给变量的是String对象引用。如果字符串常量池中已经存在这个字符串常量，则会创建一个对象，是在堆中的String对象，返回给变量的是String对象引用。

## 字符串常量池的位置

- HotSpot在JDK1.6以及之前，字符串常量池在永久代中。
- 在JDK1.7以及之后，字符串常量池移动到了堆中。

## 字符串常量池的实现

在HotSpot中，字符串常量池是一个叫做StringTable的全局的哈希表，也就是一个Hashtable。

## 字符串常量里存的是什么

- JDK1.6以及之前存放的是字符串常量
- JDK1.7以及之后存放的是字符串常量和字符串对象的引用，字符串对象存在堆中。

## String.intern()方法

intern方法可以在运行时可以通过代码生成常量并将其放入运行时常量池中。

- JDK1.6以及以前，调用intern方法，会先判断常量池中是否已经存在该字符串常量，如果存在，则直接返回该字符串常量；如果不存在，则在常量池中加入该字符串常量。
- JDK1.7以及以后，调用intern方法，会先判断常量池中是否已经存在该字符串常量，如果存在，则直接返回该字符串常量；如果不存在，说明该字符串常量在堆中，则需要把堆中该对对象的引用加入到字符串常量池中。

## 为什么要把字符串常量池移到堆中

JDK1.6以及之前字符串常量池是放在永久代，也就是方法区中的，方法区中存储类的结构信息，例如运行时常量池、字段、方法数据、构造函数、普通方法的字节码内容、还包括一些在类、实例、接口初始化时用到的特殊方法。一旦大量使用intern方法，常量池会被大量占用，会产生`java.lang.OutOfMemoryError: PermGen space`错误。

## 字符串常量池几个实例分析

```java
public class Test {
  
    public static void main(String[] args) {
        /**
         * 创建两个对象：
         * 字符串常量池中创建字符串常量：abc
         * 堆中创建一个String对象，str1指向String对象
         */
        String str1 = new String("abc");

        /**
         * 这里会先查看字符串常量池中有没有abc字符串，
         * 这里是有的，直接返回。
         * str2指向字符串常量池中的常量
         */
        String str2 = str1.intern();

        /**
         * str1指向堆中String对象
         * str2指向常量池中的常量
         * false
         */
        System.out.println(str1 == str2);

        /**
         * abc存在于字符串常量池，str3直接指向字符串常量池中的该常量
         */
        String str3 = "abc";

        /**
         * str1指向堆中String对象
         * str3指向常量池中的常量
         * false
         */
        System.out.println(str1 == str3);

        /**
         * str2指向常量池中的常量
         * str3指向常量池中的常量
         * true
         */
        System.out.println(str2 == str3);

        /**
         * 底层会使用StringBuilder将de和de拼接成dede
         * dede字符串对象在堆中创建
         * str4指向堆中的dede对象
         */
        String str4 = new String("de") + new String("de");

        /**
         * dede在字符串常量池中不存在，
         * jdk1.6以及以前，会把在字符串常量池中创建字符串常量dede，str5指向字符串常量池中
         * jdk1.7以及以后，会把堆中dede的引用放到字符串常量池中，str5指向dede的引用
         */
        String str5 = str4.intern();

        /**
         * jdk1.6以及以前：
         *  str4指向堆中的dede对象
         *  str5指向字符串常量池中
         *  所以这里是false
         *
         * jdk1.7以及以后：
         *  str4指向堆中的dede对象
         *  str5指向dede堆中的引用
         *  所以这里是true
         */
        System.out.println(str4 == str5);

        /**
         * jdk1.6以及以前，str6会指向字符串常量池中
         * jdk1.7以及以后，str6指向dede堆中的引用
         */
        String str6 = "dede";

        /**
         * jdk1.6以及以前：
         *  str4指向堆中的dede对象
         *  str6会指向字符串常量池中
         *  所以这里是false
         *
         * jdk1.7以及以后：
         *  str4指向堆中的dede对象
         *  str6指向dede堆中的引用
         *  所以这里是true
         */
        System.out.println(str4 == str6);

        /**
         * jdk1.6以及以前：
         *  str5指向字符串常量池中
         *  str6会指向字符串常量池中
         *  所以这里是true
         *
         * jdk1.7以及以后：
         *  str5指向dede堆中的引用
         *  str6指向dede堆中的引用
         *  所以这里是true
         */
        System.out.println(str5 == str6);
    }
}
```

# 包装类常量池

`Byte、Short、Integer、Long、Character、Boolean`这些包装类使用了常量池技术，对应值在`-128~127`时可以使用常量池，也就是缓存。

`Float，Double`这两个包装类没有使用常量池。

# 总结

- 字符串常量池，全局只有一个，存放字符串常量
- Class文件常量池，每个class文件都有一个，编译阶段生成，存放编译器生成的字面量和符号引用
- 运行时常量池，每个class都有一个运行时常量池，在类加载的时候会把Class文件常量池中的信息放到运行时常量池，解析阶段会把符号引用替换为直接引用。