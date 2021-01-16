---
title: JVM中类文件结构和字节码指令示例
date: 2017-08-20 20:31:37
categories: 
- JVM
tags:
- JVM
---

JVM中类文件结构和字节码指令示例。

<!--more-->

# class文件

```java
Classfile /xxxxx/me/cxis/dcc/loader/ConfigLoaderDelegate.class
  Last modified xxxx-x-x; size 1668 bytes
  MD5 checksum 26fb18f78a9fff6fccbe73d445ee8c58
  Compiled from "ConfigLoaderDelegate.java"
public class me.cxis.dcc.loader.ConfigLoaderDelegate
  // 次版本号
  minor version: 0

  // 主版本号
  major version: 52

  // 类的访问标志
  // ACC_PUBLIC：是否public类型
  // ACC_SUPER：是否允许使用invokespecial字节码指令的新语意，JDK1.0.2之后编译出来的类的这个标志都必须为真
  flags: ACC_PUBLIC, ACC_SUPER

// 常量池，存放字面量和符号引用
// 字面量类似Java中的常量，如文本字符串、final常量等
// 符号引用包括：类和接口的全限定名、字段的名称和描述符、方法的名称和描述符
Constant pool:
   // 方法的符号引用，指向第12和第42个常量
   // 最后结果是：java/lang/Object."<init>":()V
   // 调用父类Object的构造方法，该方法返回值是V表示void
   #1 = Methodref          #12.#42        // java/lang/Object."<init>":()V

   // 字段的符号引用，指向第7和第43常量
   // 最后结果是：me/cxis/dcc/loader/ConfigLoaderDelegate.configLoader:Lme/cxis/dcc/loader/ConfigLoader;
   // 对应源码的：private ConfigLoader configLoader;
   #2 = Fieldref           #7.#43         // me/cxis/dcc/loader/ConfigLoaderDelegate.configLoader:Lme/cxis/dcc/loader/ConfigLoader;

   // 类或接口的符号引用，指向第44个常量：me/cxis/dcc/loader/ZookeeperConfigLoader
   #3 = Class              #44            // me/cxis/dcc/loader/ZookeeperConfigLoader

   // 方法的符号引用，指向第3和第42个常量
   // 最后结果是：me/cxis/dcc/loader/ZookeeperConfigLoader."<init>":()V
   // 对应源码中无参构造方法
   #4 = Methodref          #3.#42         // me/cxis/dcc/loader/ZookeeperConfigLoader."<init>":()V

   // 方法的符号引用，指向第7和第45个常量
   // 最后结果是：me/cxis/dcc/loader/ConfigLoaderDelegate.getInstance:(Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;
   // 对应源码中的带参数的静态方法：public static ConfigLoaderDelegate getInstance(ConfigLoader configLoader)
   // 方法参数：Lme/cxis/dcc/loader/ConfigLoader; L表示是对象类型
   // 方法返回值：Lme/cxis/dcc/loader/ConfigLoaderDelegate;
   #5 = Methodref          #7.#45         // me/cxis/dcc/loader/ConfigLoaderDelegate.getInstance:(Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;

   // 字段的符号引用，指向第7和第46常量
   // 最后结果是：me/cxis/dcc/loader/ConfigLoaderDelegate.configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;
   // 对应源码的：private static volatile ConfigLoaderDelegate configLoaderDelegate;
   #6 = Fieldref           #7.#46         // me/cxis/dcc/loader/ConfigLoaderDelegate.configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;

   // 类或接口的符号引用，指向第47个常量：me/cxis/dcc/loader/ConfigLoaderDelegate
   #7 = Class              #47            // me/cxis/dcc/loader/ConfigLoaderDelegate

   // 方法的符号引用，指向第7和第48个常量
   // 最后结果是：me/cxis/dcc/loader/ConfigLoaderDelegate."<init>":(Lme/cxis/dcc/loader/ConfigLoader;)V
   // 对应源码中的有参构造方法：private ConfigLoaderDelegate(ConfigLoader configLoader)
   // 参数是：Lme/cxis/dcc/loader/ConfigLoader; 返回值是：V
   #8 = Methodref          #7.#48         // me/cxis/dcc/loader/ConfigLoaderDelegate."<init>":(Lme/cxis/dcc/loader/ConfigLoader;)V

   // 接口中方法的符号引用，指向第49和50个常量
   // 最后结果是：me/cxis/dcc/loader/ConfigLoader.get:(Ljava/lang/String;)Ljava/lang/String;
   // 表示的是ConfigLoader.get方法，参数是String类型，返回值是String类型
   #9 = InterfaceMethodref #49.#50        // me/cxis/dcc/loader/ConfigLoader.get:(Ljava/lang/String;)Ljava/lang/String;

  // 接口中方法的符号引用，指向第49和51个常量
  // 最后结果是：me/cxis/dcc/loader/ConfigLoader.addConfigListener:(Lme/cxis/dcc/listener/ConfigListener;)V
  // 表示的是ConfigLoader.addConfigListener方法，参数是ConfigListener类型，返回值是V
  #10 = InterfaceMethodref #49.#51        // me/cxis/dcc/loader/ConfigLoader.addConfigListener:(Lme/cxis/dcc/listener/ConfigListener;)V

  // 接口中方法的符号引用，指向第49和52个常量
  // 最后结果是：me/cxis/dcc/loader/ConfigLoader.addConfigListener:(Ljava/lang/String;Lme/cxis/dcc/listener/ConfigListener;)V
  // 表示的是ConfigLoader.addConfigListener方法，参数是String类型和ConfigListener类型，返回值是V
  #11 = InterfaceMethodref #49.#52        // me/cxis/dcc/loader/ConfigLoader.addConfigListener:(Ljava/lang/String;Lme/cxis/dcc/listener/ConfigListener;)V

  // 类或接口的符号引用，指向第53个常量：java/lang/Object
  #12 = Class              #53            // java/lang/Object

  // UTF-8编码的字符串，表示configLoader变量的名字
  #13 = Utf8               configLoader

  // UTF-8编码的字符串，ConfigLoader的描述符
  #14 = Utf8               Lme/cxis/dcc/loader/ConfigLoader;

  // UTF-8编码的字符串，configLoaderDelegate变量的名字
  #15 = Utf8               configLoaderDelegate

  // UTF-8编码的字符串，ConfigLoaderDelegate类描述符
  #16 = Utf8               Lme/cxis/dcc/loader/ConfigLoaderDelegate;

  // UTF-8编码的字符串，系统生成实例的初始化方法名字
  #17 = Utf8               <init>

  // UTF-8编码的字符串，方法的描述符常量，无参空返回
  #18 = Utf8               ()V

  // UTF-8编码的字符串，Code属性，用在方法表中，Java代码编译成的字节码指令
  #19 = Utf8               Code

  // UTF-8编码的字符串，LineNumberTable属性，用在Code属性中，Java源码的行号与字节码指令的对应关系
  #20 = Utf8               LineNumberTable

  // UTF-8编码的字符串，LocalVariableTable属性，用在Code属性中，方法的局部变量描述
  #21 = Utf8               LocalVariableTable

  // UTF-8编码的字符串，当前实例this
  #22 = Utf8               this

  // UTF-8编码的字符串，方法的描述符常量，参数为ConfigLoader返回值为V
  #23 = Utf8               (Lme/cxis/dcc/loader/ConfigLoader;)V

  // UTF-8编码的字符串，方法表，JDK8新增属性，用于支持将方法名编译进Class文件并可运行时获取
  #24 = Utf8               MethodParameters

  // UTF-8编码的字符串，方法名称常量
  #25 = Utf8               getInstance

  // UTF-8编码的字符串，方法的描述符常量，参数为空，返回值为ConfigLoaderDelegate
  #26 = Utf8               ()Lme/cxis/dcc/loader/ConfigLoaderDelegate;

  // UTF-8编码的字符串，方法的描述符常量，参数为ConfigLoader，返回值为ConfigLoaderDelegate
  #27 = Utf8               (Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;

  // UTF-8编码的字符串，StackMapTable属性，用在Code属性中，JDK6新增属性，
  // 供新的类型检查验证器检查和处理目标方法的局部变量和操作数栈所需要的类型是否匹配
  #28 = Utf8               StackMapTable

  // 类或接口的符号引用，指向第53个常量：java/lang/Object
  #29 = Class              #53            // java/lang/Object

  // 类或接口的符号引用，指向第54个常量：java/lang/Throwable
  #30 = Class              #54            // java/lang/Throwable

  // UTF-8编码的字符串，方法名称常量
  #31 = Utf8               get

  // UTF-8编码的字符串，方法的描述符常量，参数为String返回值为String
  #32 = Utf8               (Ljava/lang/String;)Ljava/lang/String;

  // UTF-8编码的字符串，// TODO
  #33 = Utf8               key

  // UTF-8编码的字符串，String类描述符
  #34 = Utf8               Ljava/lang/String;

  // UTF-8编码的字符串，方法名称常量
  #35 = Utf8               addConfigListener

  // UTF-8编码的字符串，方法的描述符常量
  #36 = Utf8               (Lme/cxis/dcc/listener/ConfigListener;)V

  // UTF-8编码的字符串，// TODO
  #37 = Utf8               configListener

  // UTF-8编码的字符串，ConfigListener接口描述符
  #38 = Utf8               Lme/cxis/dcc/listener/ConfigListener;

  // UTF-8编码的字符串，方法的描述符常量
  #39 = Utf8               (Ljava/lang/String;Lme/cxis/dcc/listener/ConfigListener;)V

  // UTF-8编码的字符串，SourceFile属性，用在类文件，记录源文件名称
  #40 = Utf8               SourceFile

  // UTF-8编码的字符串，源文件名称
  #41 = Utf8               ConfigLoaderDelegate.java

  // 字段或方法的部分符号引用，指向第17和第18个常量
  // "<init>" 方法名 ()参数为空 V返回值为void
  #42 = NameAndType        #17:#18        // "<init>":()V

  // 字段或方法的部分符号引用，指向第13和第14个常量
  // configLoader变量名，Lme/cxis/dcc/loader/ConfigLoader;变量类型
  #43 = NameAndType        #13:#14        // configLoader:Lme/cxis/dcc/loader/ConfigLoader;

  // UTF-8编码的字符串，ZookeeperConfigLoader类描述符
  #44 = Utf8               me/cxis/dcc/loader/ZookeeperConfigLoader

  // 字段或方法的部分符号引用，指向第25和第27个常量
  // getInstance方法名，(Lme/cxis/dcc/loader/ConfigLoader;) 参数，Lme/cxis/dcc/loader/ConfigLoaderDelegate;返回值
  #45 = NameAndType        #25:#27        // getInstance:(Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;

  // 字段或方法的部分符号引用，configLoaderDelegate常量
  #46 = NameAndType        #15:#16        // configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;

  // UTF-8编码的字符串，类描述符
  #47 = Utf8               me/cxis/dcc/loader/ConfigLoaderDelegate

  // 字段或方法的部分符号引用，有参构造方方法
  #48 = NameAndType        #17:#23        // "<init>":(Lme/cxis/dcc/loader/ConfigLoader;)V

  //  类或接口的符号引用，指向第55个常量：me/cxis/dcc/loader/ConfigLoader
  #49 = Class              #55            // me/cxis/dcc/loader/ConfigLoader

  // 字段或方法的部分符号引用，get方法
  #50 = NameAndType        #31:#32        // get:(Ljava/lang/String;)Ljava/lang/String;

  // 字段或方法的部分符号引用，addConfigListener方法
  #51 = NameAndType        #35:#36        // addConfigListener:(Lme/cxis/dcc/listener/ConfigListener;)V

  // 字段或方法的部分符号引用，addConfigListener方法
  #52 = NameAndType        #35:#39        // addConfigListener:(Ljava/lang/String;Lme/cxis/dcc/listener/ConfigListener;)V

  // UTF-8编码的字符串，Object类描述符
  #53 = Utf8               java/lang/Object

  // UTF-8编码的字符串，Throwable描述符
  #54 = Utf8               java/lang/Throwable

  // UTF-8编码的字符串，ConfigLoader类描述符
  #55 = Utf8               me/cxis/dcc/loader/ConfigLoader
{
  // configLoader变量，类型ConfigLoader，访问标示符private，
  private me.cxis.dcc.loader.ConfigLoader configLoader;
    descriptor: Lme/cxis/dcc/loader/ConfigLoader;
    flags: ACC_PRIVATE

  // configLoaderDelegate变量，类型ConfigLoaderDelegate，访问标示符private，static，volatile
  private static volatile me.cxis.dcc.loader.ConfigLoaderDelegate configLoaderDelegate;
    descriptor: Lme/cxis/dcc/loader/ConfigLoaderDelegate;
    flags: ACC_PRIVATE, ACC_STATIC, ACC_VOLATILE

  // 无参构造方法
  private me.cxis.dcc.loader.ConfigLoaderDelegate();
    // 无参数，返回值void
    descriptor: ()V
    // 方法访问标志private
    flags: ACC_PRIVATE
    // Code属性
    Code:
      // stack：最大操作数栈，JVM运行时会根据这个值来分配栈帧中的操作栈深度，此处为1
    
      // locals：局部变量所需的存储空间，单位是Slot变量槽，
      // byte、char、float、int、short、boolean和returnAddress等长度不超过32位的数据类型，每个局部变量占用一个变量槽，
      // 而double和long这两种64位的数据类型则需要两个变量槽来存放
      // 方法参数（包括实例方法中的隐藏参数“this”）、显式异常处理程序的参数（Exception HandlerParameter，就是try-catch语句中catch块中所定义的异常）、
      // 方法体中定义的局部变量都需要依赖局部变量表来存放

      // args_size：方法参数个数，此处的1是指实例方法的隐式参数this
      stack=1, locals=1, args_size=1
         // 将第1个局部变量槽中引用类型的本地变量推送到操作数栈顶
         // 从下面的局部变量表LocalVariableTable中可以看到只有一个this
         0: aload_0

         // invokespecial：调用父类构造方法、实例初始化方法、私有方法
         // #1在常量池中是一个方法引用，指向：java/lang/Object."<init>":()V
         // 这里就是调用父类Object的构造方法，方法的接收者是上一步aload_0推送到栈顶的对象，就是this当前对象
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V

         // return：从方法返回，返回值是void
         4: return
      // 源码行号和字节码行号对应关系
      LineNumberTable:
        line 12: 0
        line 14: 4
      // 栈帧中局部变量与源码中定义的变量之间的关系
      LocalVariableTable:
        // Start： 局部变量的生命周期开始的字节码偏移量，也就是该局部变量在哪一行开始可见
        // Length：作用范围覆盖的长度，也就是可见行数
        // Slot：代表这个局部变量所在栈帧的局部变量表槽的位置
        // Name：局部变量名称，这里是隐式参数this
        // Signature：局部变量描述符，这里是当前ConfigLoaderDelegate类
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lme/cxis/dcc/loader/ConfigLoaderDelegate;

  // 带参数的构造方法
  private me.cxis.dcc.loader.ConfigLoaderDelegate(me.cxis.dcc.loader.ConfigLoader);
    // 参数ConfigLoader，返回值void
    descriptor: (Lme/cxis/dcc/loader/ConfigLoader;)V
    // 访问标示符：private
    flags: ACC_PRIVATE
    // Code属性
    Code:
      // 最大操作数栈，2
      // 两个本地变量槽
      // 两个参数
      stack=2, locals=2, args_size=2
         // 将第1个局部变量槽中引用类型本地变量推送到栈顶
         0: aload_0
         
         // 调用父类的构造方法
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         
         // 将第1个局部变量槽中引用类型本地变量推送到栈顶，this
         4: aload_0
         // 将第2个局部变量槽中引用类型本地变量推送到栈顶，参数configLoader
         5: aload_1

         // 为指定类的实例域赋值，#2是字段引用
         6: putfield      #2                  // Field configLoader:Lme/cxis/dcc/loader/ConfigLoader;

         // 返回 void
         9: return
      // 行号表
      LineNumberTable:
        line 16: 0
        line 17: 4
        line 18: 9
      // 本地变量表
      LocalVariableTable:
        // this和configLoader两个局部变量
        Start  Length  Slot  Name   Signature
            0      10     0  this   Lme/cxis/dcc/loader/ConfigLoaderDelegate;
            0      10     1 configLoader   Lme/cxis/dcc/loader/ConfigLoader;
    // JDK8新增，用在方法表中的变长属性，作用是记录方法的各个形参名称和信息
    // 这里形参是configLoader
    MethodParameters:
      Name                           Flags
      configLoader

  // getInstance方法
  public static me.cxis.dcc.loader.ConfigLoaderDelegate getInstance();
    // 无参，返回值：ConfigLoaderDelegate
    descriptor: ()Lme/cxis/dcc/loader/ConfigLoaderDelegate;
    // 访问标示符 public static
    flags: ACC_PUBLIC, ACC_STATIC
    // Code属性
    Code:
      // 最大操作数栈深度2
      // 本地变量0
      // 参数0
      stack=2, locals=0, args_size=0
         // 创建一个对象并将其引用压入栈顶，创建ZookeeperConfigLoader对象
         0: new           #3                  // class me/cxis/dcc/loader/ZookeeperConfigLoader
         // 复制栈顶数值，并将复制值压入栈顶
         3: dup
         // 调用ZookeeperConfigLoader的构造方法
         4: invokespecial #4                  // Method me/cxis/dcc/loader/ZookeeperConfigLoader."<init>":()V
         // 调用静态方法getInstance
         7: invokestatic  #5                  // Method getInstance:(Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;
        // 返回对象引用
        10: areturn
      // 行号表
      LineNumberTable:
        line 21: 0

  // getInstance方法
  public static me.cxis.dcc.loader.ConfigLoaderDelegate getInstance(me.cxis.dcc.loader.ConfigLoader);
    // 参数ConfigLoader，返回值ConfigLoaderDelegate
    descriptor: (Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;
    // 访问标示符public static
    flags: ACC_PUBLIC, ACC_STATIC
    // Code属性
    Code:
      // 最大操作数栈深度3
      // 本地变量表3
      // 参数1个
      stack=3, locals=3, args_size=1
         // 获取#6指向的静态域configLoaderDelegate，并将其压入栈顶
         0: getstatic     #6                  // Field configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;

         // 栈顶数据出栈，判断栈顶引用是否为null，不为null时跳转到38，38是获取#6指向的静态域configLoaderDelegate，并将其压入栈顶
         3: ifnonnull     38

         // 将#7从常量池中推送到栈顶，这个是锁对象
         6: ldc           #7                  // class me/cxis/dcc/loader/ConfigLoaderDelegate
        
         // 复制栈顶数值，并将复制值压入栈顶，这个是备份锁对象
         8: dup
        
         // 将栈顶的引用推送到第二个本地变量槽，将备份的锁对象放到本地变量表中
         9: astore_1
        
        // 获得对象的锁, 用于同步方法或同步块，此时栈中的锁对象会出栈
        10: monitorenter
        
        // 获取#6指向的静态域configLoaderDelegate，并将其压入栈顶
        11: getstatic     #6                  // Field configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;
        
        // 不为null时跳转到28
        14: ifnonnull     28

        // 创建一个对象并将其引用压入栈顶，创建ConfigLoaderDelegate对象
        17: new           #7                  // class me/cxis/dcc/loader/ConfigLoaderDelegate

        // 复制栈顶数值，并将复制值压入栈顶
        20: dup

        // 将第一个本地变量槽中的引用推送到栈顶，configLoader
        21: aload_0

        // invokespecial：调用父类构造方法、实例初始化方法、私有方法，也就是调用ConfigLoaderDelegate带参的构造方法
        22: invokespecial #8                  // Method "<init>":(Lme/cxis/dcc/loader/ConfigLoader;)V

        // 为指定类的静态域赋值，configLoaderDelegate域赋值刚才new的ConfigLoaderDelegate对象
        25: putstatic     #6                  // Field configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;

        // 将第二个本地变量槽中的引用推送到栈顶，引用是ConfigLoaderDelegate，就是锁对象的备份
        28: aload_1

        // 释放对象的锁, 用于同步方法或同步块，将锁对象出栈
        29: monitorexit

        // 无条件跳转到38
        30: goto          38

        // 将栈顶的引用推送到第三个本地变量槽
        33: astore_2

        // 将第二个本地变量槽中的引用推送到栈顶，锁对象的备份
        34: aload_1

        // 释放对象的锁, 用于同步方法或同步块，将锁对象出栈
        35: monitorexit

        // 将第三个本地变量槽中的引用推送到栈顶
        36: aload_2

        // 将栈顶的异常抛出
        37: athrow

        // 获取#6指向的静态域configLoaderDelegate，并将其压入栈顶
        38: getstatic     #6                  // Field configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;

        // 返回对象引用
        41: areturn

      // 异常表
      Exception table:
         // from：起始行
         // to：结束行，但不包括结束行
         // target：跳转到继续处理的行
         // 要处理的异常情况，any表示任意异常
         from    to  target type
            11    30    33   any
            33    36    33   any
      
      // 行号表
      LineNumberTable:
        line 25: 0
        line 26: 6
        line 27: 11
        line 28: 17
        line 30: 28
        line 32: 38
      // 本地变量表
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      42     0 configLoader   Lme/cxis/dcc/loader/ConfigLoader;

      // JDK6新增，是一个变长属性，位于Code属性中，这个属性会在虚拟机类加载的字节码验证阶段被新类型检查验证器（Type Checker）使用
      // 目的在于代替以前比较消耗性能的基于数据流分析的类型推导验证器。
      // 包含零至多个栈映射帧（Stack Map Frame），每个栈映射帧都显式或隐式地代表了一个字节码偏移量，用于表示执行到该字节码时局部变量表和操作数栈的验证类型
      // TODO
      StackMapTable: number_of_entries = 3
        frame_type = 252 /* append */
          offset_delta = 28
          locals = [ class java/lang/Object ]
        frame_type = 68 /* same_locals_1_stack_item */
          stack = [ class java/lang/Throwable ]
        frame_type = 250 /* chop */
          offset_delta = 4

    // JDK8新增，用在方法表中的变长属性，作用是记录方法的各个形参名称和信息
    // 这里形参是configLoader
    MethodParameters:
      Name                           Flags
      configLoader

  // get方法
  public java.lang.String get(java.lang.String);
    // 参数String，返回值String
    descriptor: (Ljava/lang/String;)Ljava/lang/String;
    // 访问标识public
    flags: ACC_PUBLIC
    // Code属性
    Code:
      // 最大操作数栈深度2
      // 本地变量2
      // 参数2
      stack=2, locals=2, args_size=2
         // 将第一个本地变量槽中的引用推送到栈顶，本地变量槽中第一个是ConfigLoaderDelegate引用
         0: aload_0

         // 获取指定类的实例域, 并将其压入栈顶， 也就是获取实例域configLoader
         1: getfield      #2                  // Field configLoader:Lme/cxis/dcc/loader/ConfigLoader;

         // 将第二个本地变量槽中的引用推送到栈顶，本地变量槽中第二个是形参String
         4: aload_1

         // 调用接口方法，ConfigLoader的get方法
         5: invokeinterface #9,  2            // InterfaceMethod me/cxis/dcc/loader/ConfigLoader.get:(Ljava/lang/String;)Ljava/lang/String;

        // 返回对象引用
        10: areturn
      // 行号表
      LineNumberTable:
        line 41: 0
      // 本地变量表
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      11     0  this   Lme/cxis/dcc/loader/ConfigLoaderDelegate;
            0      11     1   key   Ljava/lang/String;
    // JDK8新增，用在方法表中的变长属性，作用是记录方法的各个形参名称和信息
    // 这里形参是key
    MethodParameters:
      Name                           Flags
      key

  // addConfigListener方法
  public void addConfigListener(me.cxis.dcc.listener.ConfigListener);
    // 参数ConfigListener，返回值void
    descriptor: (Lme/cxis/dcc/listener/ConfigListener;)V
    // 访问标示符public
    flags: ACC_PUBLIC
    // Code属性
    Code:
      // 操作数栈最大深度2
      // 本地变量表2
      // 参数两个
      stack=2, locals=2, args_size=2
         // 将第一个本地变量槽中的引用推送到栈顶，本地变量槽中第一个是ConfigLoaderDelegate引用
         0: aload_0

         // 获取指定类的实例域, 并将其压入栈顶， 也就是获取实例域configLoader
         1: getfield      #2                  // Field configLoader:Lme/cxis/dcc/loader/ConfigLoader;

         // 将第二个本地变量槽中的引用推送到栈顶，本地变量槽中第二个是形参ConfigListener
         4: aload_1

         // 调用接口方法，ConfigLoader的addConfigListener方法
         5: invokeinterface #10,  2           // InterfaceMethod me/cxis/dcc/loader/ConfigLoader.addConfigListener:(Lme/cxis/dcc/listener/ConfigListener;)V

        // 返回void
        10: return
      // 行号表
      LineNumberTable:
        line 45: 0
        line 46: 10
      // 本地变量表
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      11     0  this   Lme/cxis/dcc/loader/ConfigLoaderDelegate;
            0      11     1 configListener   Lme/cxis/dcc/listener/ConfigListener;
    // JDK8新增，用在方法表中的变长属性，作用是记录方法的各个形参名称和信息
    // 这里形参是configListener
    MethodParameters:
      Name                           Flags
      configListener

  // addConfigListener方法
  public void addConfigListener(java.lang.String, me.cxis.dcc.listener.ConfigListener);
    // 参数ConfigListener，返回值void
    descriptor: (Ljava/lang/String;Lme/cxis/dcc/listener/ConfigListener;)V
    // 访问标示符public
    flags: ACC_PUBLIC
    // Code属性
    Code:
      // 操作数栈最大深度3
      // 本地变量表3
      // 参数三个
      stack=3, locals=3, args_size=3
         // 将第一个本地变量槽中的引用推送到栈顶，本地变量槽中第一个是ConfigLoaderDelegate引用
         0: aload_0
         
         // 获取指定类的实例域, 并将其压入栈顶， 也就是获取实例域configLoader
         1: getfield      #2                  // Field configLoader:Lme/cxis/dcc/loader/ConfigLoader;
         
         // 将第二个本地变量槽中的引用推送到栈顶，本地变量槽中第二个是形参String
         4: aload_1

         // 将第三个本地变量槽中的引用推送到栈顶，本地变量槽中第三个是形参ConfigListener
         5: aload_2

         // 调用接口方法，ConfigLoader的addConfigListener方法
         6: invokeinterface #11,  3           // InterfaceMethod me/cxis/dcc/loader/ConfigLoader.addConfigListener:(Ljava/lang/String;Lme/cxis/dcc/listener/ConfigListener;)V

        // 返回void
        11: return
      // 行号表
      LineNumberTable:
        line 49: 0
        line 50: 11
      // 本地变量表
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      12     0  this   Lme/cxis/dcc/loader/ConfigLoaderDelegate;
            0      12     1   key   Ljava/lang/String;
            0      12     2 configListener   Lme/cxis/dcc/listener/ConfigListener;
    
    // JDK8新增，用在方法表中的变长属性，作用是记录方法的各个形参名称和信息
    // 这里形参是key和configListener
    MethodParameters:
      Name                           Flags
      key
      configListener
}
SourceFile: "ConfigLoaderDelegate.java"
```

# 字节码

```reStructuredText
// cafe babe 魔数
// 0000 次版本号 0034 主版本号
// 0038 常量池数量58个

// 下面开始第一个常量：#1
// 0a 十进制10，Methodref，类中方法的符号引用
// 00 0c十进制12，指向常量池第#12个常量，表示声明方法的类描述符Class的索引项
// 00 2a十进制42，指向常量池第#42个常量，表示名称及类型描述符NameAndType的索引项

// 常量：#2
// 09十进制9，Fieldref，字段的符号引用
cafe babe 0000 0034 0038 0a00 0c00 2a09

// 0007 十进制7，指向常量池第#7个常量，表示声明字段的类或接口的描述符Class的索引项
// 002b 十进制43，指向常量池第#43个常量，表示名称及类型描述符NameAndType的索引项

// 常量：#3
// 07十进制7，Class，类或接口的符号引用
// 00 2c十进制44，指向常量池第#44个常量，表示全限定名常量的索引项

// 常量：#4
// 0a十进制10，Methodref，类中方法的符号引用
// 0003十进制3，指向常量池第#3个常量，表示声明方法的类描述符Class的索引项
// 002a十进制42，指向常量池第#42个常量，表示名称及类型描述符NameAndType的索引项

// 常量：#5
// 0a十进制10，Methodref，类中方法的符号引用
// 00 07十进制7，指向常量池第#7个常量，表示声明方法的类描述符Class的索引项
// 002d十进制45，指向常量池第#45个常量，表示名称及类型描述符NameAndType的索引项
0007 002b 0700 2c0a 0003 002a 0a00 0700

// 常量#6
// 09十进制9，Fieldref，字段的符号引用
// 0007 十进制7，指向常量池第#7个常量，表示声明字段的类或接口的描述符Class的索引项
// 002e 十进制46，指向常量池第#46个常量，表示名称及类型描述符NameAndType的索引项

// 常量#7
// 07十进制7，Class，类或接口的符号引用
// 00 2f十进制47，指向常量池第#47个常量，表示全限定名常量的索引项

// 常量#8
// 0a十进制10，Methodref，类中方法的符号引用
// 0007十进制7，指向常量池第#7个常量，表示声明方法的类描述符Class的索引项
// 0030十进制48，指向常量池第#48个常量，表示名称及类型描述符NameAndType的索引项

// 常量#9
// 0b十进制11，InterfaceMethodref，接口中方法的符号引用
// 0031十进制49，指向常量池第#49个常量，表示声明方法的接口描述符Class索引项
2d09 0007 002e 0700 2f0a 0007 0030 0b00

// 00 32十进制50，指向常量池第#50个常量，表示名称及类型描述符NameAndType的索引项

// 常量#10
// 0b十进制11，InterfaceMethodref，接口中方法的符号引用
// 0031十进制49，指向常量池第#49个常量，表示声明方法的接口描述符Class索引项
// 0033十进制51，指向常量池第#51个常量，表示名称及类型描述符NameAndType的索引项

// 常量#11
// 0b十进制11，InterfaceMethodref，接口中方法的符号引用
// 00 31十进制49，指向常量池第#49个常量，表示声明方法的接口描述符Class索引项
// 00 34十进制52，指向常量池第#52个常量，表示名称及类型描述符NameAndType的索引项

// 常量#12
// 07十进制7，Class，类或接口的符号引用
// 0035十进制53，指向常量池第#53个常量，表示全限定名常量的索引项
3100 320b 0031 0033 0b00 3100 3407 0035

// 常量#13
// 01十进制1，UTF8编码字符串
// 00 0c字符串长度12
// 63 6f6e 6669 674c 6f61 6465 72将ASCII转换为字符串是configLoader

// 常量#14
// 01十进制1，UTF8编码字符串
0100 0c63 6f6e 6669 674c 6f61 6465 7201

// 0021字符串长度33
// 4c6d 652f 6378 6973 2f64 6363 2f6c
// 6f61 6465 722f 436f 6e66 6967 4c6f 6164
// 6572 3b将ASCII转换为字符串是Lme/cxis/dcc/loader/ConfigLoader;
0021 4c6d 652f 6378 6973 2f64 6363 2f6c
6f61 6465 722f 436f 6e66 6967 4c6f 6164

// 常量#15
// 01十进制1，UTF8编码字符串
// 0014字符串长度20
// 636f 6e66 6967 4c6f 6164
// 6572 4465 6c65 6761 7465将ASCII转换为字符串是configLoaderDelegate
6572 3b01 0014 636f 6e66 6967 4c6f 6164

// 常量#16
// 01十进制1，UTF8编码字符串
// 0029字符串长度41
// 4c 6d65
// 2f63 7869 732f 6463 632f 6c6f 6164 6572
// 2f43 6f6e 6669 674c 6f61 6465 7244 656c
// 6567 6174 653b将ASCII转换为字符串是Lme/cxis/dcc/loader/ConfigLoaderDelegate;
6572 4465 6c65 6761 7465 0100 294c 6d65
2f63 7869 732f 6463 632f 6c6f 6164 6572
2f43 6f6e 6669 674c 6f61 6465 7244 656c

// 常量#17
// 01十进制1，UTF8编码字符串
// 00 06字符串长度6
// 3c 696e 6974 3e将ASCII转换为字符串是<init>

// 常量#18
// 01十进制1，UTF8编码字符串
6567 6174 653b 0100 063c 696e 6974 3e01

// 0003字符串长度3
// 2829 56将ASCII转换为字符串是()V

// 常量#19
// 01十进制1，UTF8编码字符串
// 0004字符串长度4
// 436f 6465将ASCII转换为字符串是Code

// 常量#20
// 01十进制1，UTF8编码字符串
// 00 0f字符串长度15
// 4c
// 696e 654e 756d 6265 7254 6162 6c65将ASCII转换为字符串是LineNumberTable
0003 2829 5601 0004 436f 6465 0100 0f4c

// 常量#21
// 01十进制1，UTF8编码字符串
// 00 12字符串长度18
// 4c 6f63 616c 5661 7269 6162 6c65 5461
// 626c 65将ASCII转换为字符串是LocalVariableTable
696e 654e 756d 6265 7254 6162 6c65 0100
124c 6f63 616c 5661 7269 6162 6c65 5461

// 常量#22
// 01十进制1，UTF8编码字符串
// 0004字符串长度4
// 7468 6973将ASCII转换为字符串是this

// 常量#23
// 01十进制1，UTF8编码字符串
// 00 24 字符串长度36
// 28 4c6d
// 652f 6378 6973 2f64 6363 2f6c 6f61 6465
// 722f 436f 6e66 6967 4c6f 6164 6572 3b29
// 56 将ASCII转换为字符串是(Lme/cxis/dcc/loader/ConfigLoader;)V
626c 6501 0004 7468 6973 0100 2428 4c6d
652f 6378 6973 2f64 6363 2f6c 6f61 6465
722f 436f 6e66 6967 4c6f 6164 6572 3b29

// 常量#24
// 01十进制1，UTF8编码字符串
// 0010 字符串长度16
// 4d65 7468 6f64 5061 7261 6d65
// 7465 7273 将ASCII转换为字符串是MethodParameters
5601 0010 4d65 7468 6f64 5061 7261 6d65

// 常量#25
// 01十进制1，UTF8编码字符串
// 00 0b 字符串长度11
// 67 6574 496e 7374 616e
// 6365 将ASCII转换为字符串是 getInstance
7465 7273 0100 0b67 6574 496e 7374 616e

// 常量#26
// 01十进制1，UTF8编码字符串
// 00 2b 字符串长度43
// 28 294c 6d65 2f63 7869 732f
// 6463 632f 6c6f 6164 6572 2f43 6f6e 6669
// 674c 6f61 6465 7244 656c 6567 6174 653b 将ASCII转换为字符串是 ()Lme/cxis/dcc/loader/ConfigLoaderDelegate;
6365 0100 2b28 294c 6d65 2f63 7869 732f
6463 632f 6c6f 6164 6572 2f43 6f6e 6669
674c 6f61 6465 7244 656c 6567 6174 653b

// 常量#27
// 01十进制1，UTF8编码字符串
// 00 4c 字符串长度76
// 28 4c6d 652f 6378 6973 2f64 6363
// 2f6c 6f61 6465 722f 436f 6e66 6967 4c6f
// 6164 6572 3b29 4c6d 652f 6378 6973 2f64
// 6363 2f6c 6f61 6465 722f 436f 6e66 6967
// 4c6f 6164 6572 4465 6c65 6761 7465 3b 将ASCII转换为字符串是 (Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;
0100 4c28 4c6d 652f 6378 6973 2f64 6363
2f6c 6f61 6465 722f 436f 6e66 6967 4c6f
6164 6572 3b29 4c6d 652f 6378 6973 2f64
6363 2f6c 6f61 6465 722f 436f 6e66 6967

// 常量#28
// 01十进制1，UTF8编码字符串
4c6f 6164 6572 4465 6c65 6761 7465 3b01

// 000d 字符串长度13
// 5374 6163 6b4d 6170 5461 626c 65 将ASCII转换为字符串是 StackMapTable

// 常量#29
// 07 十进制7，Class，类或接口的符号引用
000d 5374 6163 6b4d 6170 5461 626c 6507

// 0035 十进制53，指向常量池第#53个常量，表示全限定名常量的索引项

// 常量#30
// 07十进制7，Class，类或接口的符号引用
// 00 36 十进制54，指向常量池第#54个常量，表示全限定名常量的索引项

// 常量#31
// 01十进制1，UTF8编码字符串
// 0003 字符串长度3
// 6765 74 将ASCII转换为字符串是 get

// 常量#32
// 01十进制1，UTF8编码字符串
// 0026 字符串长度38
// 284c
// 6a61 7661 2f6c 616e 672f 5374 7269 6e67
// 3b29 4c6a 6176 612f 6c61 6e67 2f53 7472
// 696e 673b 将ASCII转换为字符串是 (Ljava/lang/String;)Ljava/lang/String;
0035 0700 3601 0003 6765 7401 0026 284c
6a61 7661 2f6c 616e 672f 5374 7269 6e67
3b29 4c6a 6176 612f 6c61 6e67 2f53 7472

// 常量#33
// 01十进制1，UTF8编码字符串
// 00 03 字符串长度3
// 6b 6579 将ASCII转换为字符串是 key

// 常量#34
// 01十进制1，UTF8编码字符串
// 00 12 字符串长度18
// 4c 6a61
// 7661 2f6c 616e 672f 5374 7269 6e67 3b 将ASCII转换为字符串是 Ljava/lang/String;
696e 673b 0100 036b 6579 0100 124c 6a61

// 常量#35
// 01十进制1，UTF8编码字符串
7661 2f6c 616e 672f 5374 7269 6e67 3b01

// 0011 字符串长度17
// 6164 6443 6f6e 6669 674c 6973 7465
// 6e65 72 将ASCII转换为字符串是 addConfigListener
0011 6164 6443 6f6e 6669 674c 6973 7465

// 常量#36
// 01十进制1，UTF8编码字符串
// 0028 字符串长度40
// 284c 6d65 2f63 7869 732f
// 6463 632f 6c69 7374 656e 6572 2f43 6f6e
// 6669 674c 6973 7465 6e65 723b 2956 将ASCII转换为字符串是 (Lme/cxis/dcc/listener/ConfigListener;)V
6e65 7201 0028 284c 6d65 2f63 7869 732f
6463 632f 6c69 7374 656e 6572 2f43 6f6e

// 常量#37
// 01十进制1，UTF8编码字符串
// 00 0e 字符串长度14
6669 674c 6973 7465 6e65 723b 2956 0100

// 63 6f6e 6669 674c 6973 7465 6e65 72 将ASCII转换为字符串是 configListener

// 常量#38
// 01十进制1，UTF8编码字符串
0e63 6f6e 6669 674c 6973 7465 6e65 7201

// 0025 字符串长度37
// 4c6d 652f 6378 6973 2f64 6363 2f6c
// 6973 7465 6e65 722f 436f 6e66 6967 4c69
// 7374 656e 6572 3b 将ASCII转换为字符串是 Lme/cxis/dcc/listener/ConfigListener;
0025 4c6d 652f 6378 6973 2f64 6363 2f6c
6973 7465 6e65 722f 436f 6e66 6967 4c69

// 常量#39
// 01十进制1，UTF8编码字符串
// 003a 字符串长度58
// 284c 6a61 7661
// 2f6c 616e 672f 5374 7269 6e67 3b4c 6d65
// 2f63 7869 732f 6463 632f 6c69 7374 656e
// 6572 2f43 6f6e 6669 674c 6973 7465 6e65
// 723b 2956 将ASCII转换为字符串是 (Ljava/lang/String;Lme/cxis/dcc/listener/ConfigListener;)V
7374 656e 6572 3b01 003a 284c 6a61 7661
2f6c 616e 672f 5374 7269 6e67 3b4c 6d65
2f63 7869 732f 6463 632f 6c69 7374 656e
6572 2f43 6f6e 6669 674c 6973 7465 6e65

// 常量#40
// 01十进制1，UTF8编码字符串
// 00 0a 字符串长度10
// 284c 6a61 7661
// 53 6f75 7263 6546 696c
// 65 将ASCII转换为字符串是 SourceFile
723b 2956 0100 0a53 6f75 7263 6546 696c

// 常量#41
// 01十进制1，UTF8编码字符串
// 0019 字符串长度25
// 436f 6e66 6967 4c6f 6164 6572
// 4465 6c65 6761 7465 2e6a 6176 61 将ASCII转换为字符串是 ConfigLoaderDelegate.java
6501 0019 436f 6e66 6967 4c6f 6164 6572

// 常量#42
// 0c十进制12，NameAndType，字段或方法的部分符号引用
// 0011 十进制17，指向常量池第#17个常量，表示该字段或方法名称常量项索引
4465 6c65 6761 7465 2e6a 6176 610c 0011

// 0012十进制18，指向常量池第#18个常量，表示该字段或方法描述符常量项的索引

// 常量#43
// 0c十进制12，NameAndType，字段或方法的部分符号引用
// 00 0d 十进制13，指向常量池第#13个常量，表示该字段或方法名称常量项索引
// 00 0e 十进制14，指向常量池第#14个常量，表示该字段或方法描述符常量项的索引

// 常量#44
// 01十进制1，UTF8编码字符串
// 0028 字符串长度40
// 6d65 2f63 7869
// 732f 6463 632f 6c6f 6164 6572 2f5a 6f6f
// 6b65 6570 6572 436f 6e66 6967 4c6f 6164
// 6572 将ASCII转换为字符串是 me/cxis/dcc/loader/ZookeeperConfigLoader
0012 0c00 0d00 0e01 0028 6d65 2f63 7869
732f 6463 632f 6c6f 6164 6572 2f5a 6f6f
6b65 6570 6572 436f 6e66 6967 4c6f 6164

// 常量#45
// 0c十进制12，NameAndType，字段或方法的部分符号引用
// 00 19 十进制25，指向常量池第#25个常量，表示该字段或方法名称常量项索引
// 00 1b 十进制27，指向常量池第#27个常量，表示该字段或方法描述符常量项的索引

// 常量#46
// 0c十进制12，NameAndType，字段或方法的部分符号引用
// 000f 十进制15，指向常量池第#15个常量，表示该字段或方法名称常量项索引
// 0010 十进制16，指向常量池第#16个常量，表示该字段或方法描述符常量项的索引

// 常量#47
// 01十进制1，UTF8编码字符串
// 00 27 字符串长度39
// 6d
// 652f 6378 6973 2f64 6363 2f6c 6f61 6465
// 722f 436f 6e66 6967 4c6f 6164 6572 4465
// 6c65 6761 7465 将ASCII转换为字符串是 me/cxis/dcc/loader/ConfigLoaderDelegate
6572 0c00 1900 1b0c 000f 0010 0100 276d
652f 6378 6973 2f64 6363 2f6c 6f61 6465
722f 436f 6e66 6967 4c6f 6164 6572 4465

// 常量#48
// 0c十进制12，NameAndType，字段或方法的部分符号引用
// 00 11 十进制17，指向常量池第#17个常量，表示该字段或方法名称常量项索引
// 00 17 十进制23，指向常量池第#23个常量，表示该字段或方法描述符常量项的索引

// 常量#49
// 07十进制7，Class，类或接口的符号引用
// 0037十进制55，指向常量池第#55个常量，表示全限定名常量的索引项

// 常量#50
// 0c十进制12，NameAndType，字段或方法的部分符号引用
// 00 1f 十进制31，指向常量池第#31个常量，表示该字段或方法名称常量项索引
6c65 6761 7465 0c00 1100 1707 0037 0c00

// 00 20 十进制32，指向常量池第#32个常量，表示该字段或方法描述符常量项的索引

// 常量#51
// 0c十进制12，NameAndType，字段或方法的部分符号引用
// 0023 十进制35，指向常量池第#35个常量，表示该字段或方法名称常量项索引
// 0024 十进制36，指向常量池第#36个常量，表示该字段或方法描述符常量项的索引4

// 常量#52
// 0c十进制12，NameAndType，字段或方法的部分符号引用
// 00 23 十进制35，指向常量池第#35个常量，表示该字段或方法名称常量项索引
// 00 27 十进制39，指向常量池第#39个常量，表示该字段或方法描述符常量项的索引

// 常量#53
// 01十进制1，UTF8编码字符串
// 0010 字符串长度16
1f00 200c 0023 0024 0c00 2300 2701 0010

// 6a61 7661 2f6c 616e 672f 4f62 6a65 6374 将ASCII转换为字符串是 java/lang/Object
6a61 7661 2f6c 616e 672f 4f62 6a65 6374

// 常量#54
// 01十进制1，UTF8编码字符串
// 00 13 字符串长度19
// 6a 6176 612f 6c61 6e67 2f54 6872
// 6f77 6162 6c65 将ASCII转换为字符串是 java/lang/Throwable
0100 136a 6176 612f 6c61 6e67 2f54 6872

// 常量#55
// 01十进制1，UTF8编码字符串
// 00 1f 字符串长度31
// 6d 652f 6378 6973
// 2f64 6363 2f6c 6f61 6465 722f 436f 6e66
// 6967 4c6f 6164 6572 将ASCII转换为字符串是 me/cxis/dcc/loader/ConfigLoader
6f77 6162 6c65 0100 1f6d 652f 6378 6973
2f64 6363 2f6c 6f61 6465 722f 436f 6e66

// 0021 访问标识 ACC_PUBLIC, ACC_SUPER
// 0007 十进制7 类索引this_class 指向常量池#7索引
// 000c 十进制12 父类索引super_class 指向常量池#12索引
// 0000 十进制0 接口数量interfaces_count 这里为0
6967 4c6f 6164 6572 0021 0007 000c 0000

// 0002 十进制2 字段表数量fields_count，有两个字段
// 0002 访问标识 ACC_PRIVATE
// 000d 十进制13 字段的简单名称name_index 指向常量池#13索引
// 000e 十进制14 字段和方法的描述符descriptor_index 指向常量池#14索引
// 0000 十进制0 attributes_count

// 004a 访问标识 0x0002 | 0x0008 | 0x0040 ACC_PRIVATE, ACC_STATIC, ACC_VOLATILE
// 000f 十进制15 字段的简单名称name_index 指向常量池#15索引
// 0010 十进制16 字段和方法的描述符descriptor_index 指向常量池#16索引
0002 0002 000d 000e 0000 004a 000f 0010

// 0000 十进制0 attributes_count
// 0007 十进制7 方法表数量methods_count，有7个方法

// 第一个方法开始
// 0002 访问标识 ACC_PRIVATE
// 0011 十进制17 方法的简单名称name_index 指向常量池#17索引：<init>
// 0012 十进制18 方法的描述符descriptor_index 指向常量池#18索引：()V
// 0001 十进制1 attributes_count为1
// 0013 十进制19 属性名字索引，指向#19常量：Code
// 0000 0033 十进制51 Code属性长度51
0000 0007 0002 0011 0012 0001 0013 0000

// 0001 十进制1 max_stack最大操作数栈1
// 0001 十进制1 max_locals局部变量表1
// 0000 0005 十进制5 code_length字节码长度5个
// 2a 字节码指令 aload_0 将第一个引用类型本地变量推送到栈顶：this

// b7 字节码指令 invokespecial 调用超类构建方法、实例初始化方法、私有方法
// 0001 十进制1 常量池#1索引：java/lang/Object."<init>":()V
// 所以b7 0001 就是调用父类的构造方法

// b1 字节码指令 return 从当前方法返回void

// 00 00 exception_table_length异常表长度为0
0033 0001 0001 0000 0005 2ab7 0001 b100

// 00 02 attributes_count属性表有两个
// 00 14 十进制20 属性名字索引#20常量：LineNumberTable
// 00 0000 0a 十进制10 属性长度10
// 00 02 十进制2 line_number_table_length行号表2
// 00 00 十进制0 start_pc字节码行号0
// 00 0c 十进制12 line_number源码行号12
// 00 04 十进制4 start_pc字节码行号4
0000 0200 1400 0000 0a00 0200 0000 0c00

// 00 0e 十进制14 line_number源码行号14

// 00 15 十进制21 属性名字索引#21常量：LocalVariableTable
// 00 0000 0c 十进制12 属性长度12
// 00 01 十进制1 local_variable_table_length本地变量表1个
// 00 00 十进制0 start_pc字节码行号0
// 00 05 十进制5 length长度5
// 00 16 十进制22 name_index指向#22常量：this
0400 0e00 1500 0000 0c00 0100 0000 0500

// 00 10 十进制16 descriptor_index指向#16常量：Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// 00 00 本地变量表槽索引：第0个索引位置

// 接下来是第二个方法开始
// 0002 访问标识 ACC_PRIVATE
// 00 11 十进制17 方法的简单名称name_index 指向常量池#17索引：<init>
// 00 17 十进制23 方法的描述符descriptor_index 指向常量池#23索引：(Lme/cxis/dcc/loader/ConfigLoader;)V
// 00 02 十进制2 attributes_count为2
// 00 13 十进制19 属性名字索引，指向#19常量：Code
// 00 0000 46 十进制70 Code属性长度70
1600 1000 0000 0200 1100 1700 0200 1300

// 00 02 十进制2 max_stack最大操作数栈2
// 00 02 十进制2 max_locals局部变量表2
// 00 0000 0a 十进制10 code_length字节码长度10个
// 2a 字节码指令 aload_0 将第一个引用类型本地变量推送到栈顶：this
// b7 字节码指令 invokespecial 调用超类构建方法、实例初始化方法、私有方法
// 00 01 十进制1 常量池#1索引：java/lang/Object."<init>":()V
// 所以b7 0001 就是调用父类的构造方法
// 2a 字节码指令 aload_0 将第一个引用类型本地变量推送到栈顶：this
0000 4600 0200 0200 0000 0a2a b700 012a

// 2b 字节码指令 aload_1 将第二个引用类型本地变量推送到栈顶：参数configLoader
// b5 字节码指令 putfield 为指定类的实例域赋值
// 0002 十进制2 指向#2常量：Field configLoader:Lme/cxis/dcc/loader/ConfigLoader;
// 所以b5 0002 是为#2常量configLoader赋值，赋予的是栈顶的从本地变量加载出来的configLoader
// b1 字节码指令 return

// 00 00 异常表长度为0
// 00 02 attributes_count属性表有两个
// 00 14 十进制20 属性名字索引#20常量：LineNumberTable
// 00 0000 0e 十进制14 属性长度14
// 00 03 十进制3 行号表表有3个
2bb5 0002 b100 0000 0200 1400 0000 0e00

// 00 00 十进制0 start_pc字节码行号0
// 00 10 十进制16 line_number源码行号16
// 00 04 十进制4 start_pc字节码行号4
// 00 11 十进制17 line_number源码行号17
// 00 09 十进制9 start_pc字节码行号9
// 00 12 十进制18 line_number源码行号18

// 00 15 十进制21 属性名字索引#21常量：LocalVariableTable
// 00 0000 16 十进制22 属性长度22
0300 0000 1000 0400 1100 0900 1200 1500

// 00 02 十进制2 local_variable_table_length本地变量表2个
// 00 00 十进制0 start_pc字节码行号0
// 00 0a 十进制10 length长度10
// 00 16 十进制22 name_index指向#22常量：this
// 00 10 十进制16 descriptor_index指向#16常量：Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// 00 00 本地变量表槽索引：第0个索引位置

// 00 00 十进制0 start_pc字节码行号0
0000 1600 0200 0000 0a00 1600 1000 0000

// 00 0a 十进制10 length长度10
// 00 0d 十进制13 name_index指向#13常量：configLoader
// 00 0e 十进制14 descriptor_index指向#14常量：Lme/cxis/dcc/loader/ConfigLoader;
// 00 01 本地变量表槽索引：第1个索引位置

// 00 18 十进制24 属性名字索引#24常量：MethodParameters
// 00 0000 05 attribute_length 5
// 01 parameters_count 1
0000 0a00 0d00 0e00 0100 1800 0000 0501

// 000d 十进制13 指向#13常量：configLoader
// 0000 access_flags 无

// 第三个方法开始
// 0009 访问标识0x0001 | 0x0008 public static
// 0019 十进制25 方法的简单名称name_index 指向常量池#25索引：getInstance
// 001a 十进制26 方法的描述符descriptor_index 指向常量池#26索引：()Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// 0001 十进制1 attributes_count为1
// 0013 十进制19 属性名字索引，指向#19常量：Code
// 0000 0023 十进制35 Code属性长度35
000d 0000 0009 0019 001a 0001 0013 0000

// 0002 十进制2 max_stack最大操作数栈2
// 0000 十进制0 max_locals局部变量表0
// 0000 000b 十进制11 code_length字节码长度11个
// bb 字节码指令 new 创建一个对象，将引用压入栈顶
// 00 03 常量池#3 me/cxis/dcc/loader/ZookeeperConfigLoader，也就是new的对象
// 59 字节码指令 dup 复制栈顶数值并将复制值压入栈顶
// b7 字节码指令 invokespecial 调用超类构建方法、实例初始化方法、私有方法
// 00 04 十进制4 常量池#1索引：me/cxis/dcc/loader/ZookeeperConfigLoader."<init>":()V
// 所以b7 0004 就是调用ZookeeperConfigLoader的构造方法
0023 0002 0000 0000 000b bb00 0359 b700

// b8 字节码指令 invokestatic 调用静态方法
// 0005 常量池#5 getInstance:(Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// b8 0005 调用getInstance方法
// b0 字节码指令 areturn 从当前方法返回对象引用
// 00 00 异常表长度为0
// 00 01 attributes_count属性表有1个
// 00 14 十进制20 属性名字索引#20常量：LineNumberTable
// 00 0000 06 十进制6 属性长度6
// 00 01 十进制1 行号表有1个
04b8 0005 b000 0000 0100 1400 0000 0600

// 00 00 十进制0 start_pc字节码行号0
// 00 15 十进制21 line_number源码行号21

// 第四个方法开始
// 0009 访问标识0x0001 | 0x0008 public static
// 0019 十进制25 方法的简单名称name_index 指向常量池#25索引：getInstance
// 001b 十进制27 方法的描述符descriptor_index 指向常量池#27索引：(Lme/cxis/dcc/loader/ConfigLoader;)Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// 0002 十进制2 attributes_count为2
// 0013 十进制19 属性名字索引，指向#19常量：Code
// 00 0000 8d 十进制141 Code属性长度141
0100 0000 1500 0900 1900 1b00 0200 1300

// 00 03 十进制3 max_stack最大操作数栈3
// 00 03 十进制3 max_locals局部变量表3
// 00 0000 2a 十进制42 code_length字节码长度42个
// b2 getstatic 获取指定的静态域并压入栈顶
// 0006 #6常量：Field configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// c7 ifnonnull 不为null时跳转
// 0023 十进制35 不为null时跳转到第35字节码指令
0000 8d00 0300 0300 0000 2ab2 0006 c700

// 12 ldc 将int,float或String型常量值从常量池中推送至栈顶
// 07 #7常量：class me/cxis/dcc/loader/ConfigLoaderDelegate
// 59 dup 复制栈顶数值并将复制值压入栈顶
// 4c astore_1 将栈顶引用型数值存入第二个本地变量
// c2 monitorenter 获得对象的锁, 用于同步方法或同步块
// b2 getstatic 获取指定的静态域并压入栈顶
// 00 06 #6常量：Field configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// c7 ifnonnull 不为null时跳转
// 000e 十进制14 不为null时跳转到第14字节码指令
// bb new 创建一个对象, 并将其引用引用值压入栈顶
// 00 07 #7常量：class me/cxis/dcc/loader/ConfigLoaderDelegate
// 59 dup 复制栈顶数值并将复制值压入栈顶
2312 0759 4cc2 b200 06c7 000e bb00 0759

// 2a aload_0 将第一个引用类型本地变量推送至栈顶
// b7 invokespecial 调用超类构建方法, 实例初始化方法, 私有方法
// 0008 #8常量：Method "<init>":(Lme/cxis/dcc/loader/ConfigLoader;)V
// b7 0008 就是调用ConfigLoaderDelegate带参的构造方法
// b3 putstatic 为指定类的静态域赋值
// 00 06 #6常量：Field configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// 2b aload_1 将第二个引用类型本地变量推送至栈顶
// c3 monitorexit 释放对象的锁, 用于同步方法或同步块
// a7 goto 无条件跳转
// 0008 跳转到8
// 4d astore_2 将栈顶引用型数值存入第三个本地变量
// 2b aload_1 将第二个引用类型本地变量推送至栈顶
// c3 monitorexit 释放对象的锁, 用于同步方法或同步块
// 2c aload_2 将第三个引用类型本地变量推送至栈顶
2ab7 0008 b300 062b c3a7 0008 4d2b c32c

// bf athrow 将栈顶的异常抛出
// b2 getstatic 获取指定的静态域并压入栈顶
// 00 06 #6常量：Field configLoaderDelegate:Lme/cxis/dcc/loader/ConfigLoaderDelegate;
// b0 字节码指令 areturn 从当前方法返回对象引用
// 00 02 两个异常表
// 00 0b start_pc 11
// 00 1e end_pc 30
// 00 21 handler_pc 33
// 00 00 catch_type 0 任何异常

// 00 21 start_pc 33
bfb2 0006 b000 0200 0b00 1e00 2100 0000

// 00 24 end_pc 36
// 00 21 handler_pc 33
// 00 00 catch_type 0 任何异常
// 下面的省略。。。。
2100 2400 2100 0000 0300 1400 0000 1a00
0600 0000 1900 0600 1a00 0b00 1b00 1100
1c00 1c00 1e00 2600 2000 1500 0000 0c00
0100 0000 2a00 0d00 0e00 0000 1c00 0000
0f00 03fc 001c 0700 1d44 0700 1efa 0004
0018 0000 0005 0100 0d00 0000 0100 1f00
2000 0200 1300 0000 3f00 0200 0200 0000
0b2a b400 022b b900 0902 00b0 0000 0002
0014 0000 0006 0001 0000 0029 0015 0000
0016 0002 0000 000b 0016 0010 0000 0000
000b 0021 0022 0001 0018 0000 0005 0100
2100 0000 0100 2300 2400 0200 1300 0000
4300 0200 0200 0000 0b2a b400 022b b900
0a02 00b1 0000 0002 0014 0000 000a 0002
0000 002d 000a 002e 0015 0000 0016 0002
0000 000b 0016 0010 0000 0000 000b 0025
0026 0001 0018 0000 0005 0100 2500 0000
0100 2300 2700 0200 1300 0000 4e00 0300
0300 0000 0c2a b400 022b 2cb9 000b 0300
b100 0000 0200 1400 0000 0a00 0200 0000
3100 0b00 3200 1500 0000 2000 0300 0000
0c00 1600 1000 0000 0000 0c00 2100 2200
0100 0000 0c00 2500 2600 0200 1800 0000
0902 0021 0000 0025 0000 0001 0028 0000
0002 0029
```



# ConfigLoaderDelegate源文件

```java
public class ConfigLoaderDelegate {

    private ConfigLoader configLoader;

    private static volatile ConfigLoaderDelegate configLoaderDelegate;

    private ConfigLoaderDelegate() {

    }

    private ConfigLoaderDelegate(ConfigLoader configLoader) {
        this.configLoader = configLoader;
    }

    public static ConfigLoaderDelegate getInstance() {
        return getInstance(new ZookeeperConfigLoader());
    }

    public static ConfigLoaderDelegate getInstance(ConfigLoader configLoader) {
        if (configLoaderDelegate == null) {
            synchronized (ConfigLoaderDelegate.class) {
                if (configLoaderDelegate == null) {
                    configLoaderDelegate = new ConfigLoaderDelegate(configLoader);
                }
            }
        }
        return configLoaderDelegate;
    }

    /**
     * 根据key获取value
     * @param key ${projectName}.key
     * @return
     */
    public String get(String key) {
        return configLoader.get(key);
    }

    public void addConfigListener(ConfigListener configListener) {
        configLoader.addConfigListener(configListener);
    }

    public void addConfigListener(String key, ConfigListener configListener) {
        configLoader.addConfigListener(key, configListener);
    }
}
```

# ConfigLoader源文件

```java
public interface ConfigLoader {

    String get(String key);

    String parseKey(String key);

    void addConfigListener(ConfigListener configListener);

    void addConfigListener(String key, ConfigListener configListener);
}
```

