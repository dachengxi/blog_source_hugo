---
title: JVM中的Class类文件结构
date: 2017-08-19 17:48:33
categories: 
- JVM
tags:
- JVM
---

JVM中的Class类文件结构学习。

<!--more-->

Class文件是一组以8字节位基础单位的二进制流，使用类似C语言结构体的伪结构存储数据，只有两种数据类型：无符号数和表。

无符号数用来描述数字、索引引用、数量值或者按照UTF8编码构成的字符串值，分为：

- u1，1个字节
- u2，2个字节
- u4，4个字节
- u8，8个字节

表由多个无符号数或者其他表作为数据项，构成了复合数据类型。

文件格式如下表：

| 类型           | 名称                | 数量                    |
| -------------- | ------------------- | ----------------------- |
| u4             | magic               | 1                       |
| u2             | minor_version       | 1                       |
| u2             | major_version       | 1                       |
| u2             | constant_pool_count | 1                       |
| cp_info        | constant_pool       | constant_pool_count - 1 |
| u2             | access_flags        | 1                       |
| u2             | this_class          | 1                       |
| u2             | super_class         | 1                       |
| u2             | interfaces_count    | 1                       |
| u2             | interfaces          | interfaces_count        |
| u2             | fields_count        | 1                       |
| field_info     | fields              | fields_count            |
| u2             | methods_count       | 1                       |
| method_info    | methods             | methods_count           |
| u2             | attributes_count    | 1                       |
| attribute_info | attributes          | attributes_count        |

可以这样记忆，前面几个先记住：

- 魔数
- 次版本号
- 主版本号
- 常量池数量
- 常量池

后面几个按照我们平时写一个类的顺序来记忆：

- 访问标志
- this_class
- 父类
- 接口数量
- 接口
- 字段数量
- 字段
- 方法数量
- 方法
- 属性数量
- 属性

# 描述符

描述符用来描述：

- 字段的数据类型
- 方法的参数列表（包括数量、类型以及顺序）
- 返回值

描述符标识字符含义：

![image-20200421204704104](/JVM中的Class类文件结构/descriptor.png)

- 数组类型，每一维度使用一个前置的`[`字符来描述。
- 描述方法时，按照先参数列表，后返回值的顺序描述

# 魔数

Class文件头4个字节是魔数Magic Number，用来确定这个文件是否为一个能被虚拟机接受的Class文件，值是：0xCAFEBABE

# 主次版本号

第5个字节和6个字节是次版本号Minor Version，第7字节和8字节是主版本号Major Version。

高版本的JDK能向下兼容以前版本的Class文件，但不能运行更高版本的Class文件。

# 常量池

一个u2类型表示常量池的容量计数constant_pool_count，从1开始。

常量池主要用来存放：字面量和符号引用。

- 字面量比较接近Java语言层面的常量概念，比如：文本字符串、被声明为final的常量值等。
- 符号引用，主要包括：
  - 被模块导出或者开放的包（Package）
  - 类和接口的全限定名（Fully Qualified Name）
  - 字段的名称和描述符（Descriptor）
  - 方法的名称和描述符
  - 方法句柄和方法类型（Method Handle、Method Type、Invoke Dynamic）
  - 动态调用点和动态常量（Dynamically-Computed Call Site、Dynamically-Computed Constant）

常量池中的常量都是一个表，表的其实第一位是u1类型的标志位，代表当前常量属于哪种常量类型，共有17种常量类型：

| 类型                             | 标志 | 描述                     |
| -------------------------------- | ---- | ------------------------ |
| CONSTANT_Utf8_info               | 1    | UTF-8编码的字符串        |
| CONSTANT_Integer_info            | 3    | 整形字面量               |
| CONSTANT_Float_info              | 4    | 浮点型字面量             |
| CONSTANT_Long_info               | 5    | 长整型字面量             |
| CONSTANT_Double_info             | 6    | 双精度浮点型字面量       |
| CONSTANT_Class_info              | 7    | 类或接口的符号引用       |
| CONSTANT_String_info             | 8    | 字符串类型字面量         |
| CONSTANT_Fieldref_info           | 9    | 字段的符号引用           |
| CONSTANT_Methodref_info          | 10   | 类中方法的符号引用       |
| CONSTANT_InterfaceMethodRef_info | 11   | 接口中方法的符号引用     |
| CONSTANT_NameAndType_info        | 12   | 字段或方法的部分符号引用 |
| CONSTANT_MethodHandle_info       | 15   | 方法句柄                 |
| CONSTANT_MethodType_info         | 16   | 方法类型                 |
| CONSTANT_Dynamic_info            | 17   | 动态计算常量             |
| CONSTANT_InvokeDynamic_info      | 18   | 动态方法调用点           |
| CONSTANT_Module_info             | 19   | 模块                     |
| CONSTANT_Package_info            | 20   | 模块中开放或导出的包     |

## CONSTANT_Class_info

| 类型 | 名称       | 数量 |
| ---- | ---------- | ---- |
| u1   | tag        | 1    |
| u2   | name_index | 1    |

- tag，标志位，用来区分常量类型，CONSTANT_Class_info是7
- name_index，是常量池的索引值，指向常量池中一个CONSTANT_Utf8_info类型常量，代表这个类或接口的全限定名

## CONSTANT_Utf8_info

| 类型 | 名称   | 数量   |
| ---- | ------ | ------ |
| u1   | tag    | 1      |
| u2   | length | 1      |
| u1   | bytes  | length |

- tag，标志位，用来区分常量类型，CONSTANT_Utf8_info是1
- length，表示这个UTF-8编码的字符串长度是多少字节
- bytes，使用UTF-8缩略编码表示的字符串，长度是length

Class文件中方法、字段等都需要引用CONSTANT_Utf8_info类型常量来描述名称，所以Java中方法、字段名的最大程度就是CONSTANT_Utf8_info类型的常量的最大长度，也就是length，u2类型，最大值65535，64KB。

## 常量池数据类型结构汇总图

![常量池中的数据类型结构](/JVM中的Class类文件结构/constant-pool-1.png)

![常量池中的数据类型结构续](/JVM中的Class类文件结构/constant-pool-2.png)

# 访问标志

常量池后面两个字节表示访问标志，access_flags，表示类或接口层次的访问信息，包括是类还是接口、是否public类型、是否abstract类型，类是否是final等等。

![image-20200421201933205](/JVM中的Class类文件结构/access_flags.png)

# this_class

this_class类索引，u2类型，用来确定这个类的全限定名，指向一个类型为CONSTANT_Class_info的类描述符常量，通过CONSTANT_Class_info中的索引值可以找到CONSTANT_Utf8_info类型的常量中的全限定名字符串。

# super_class

super_class父类索引，u2类型，用来确定父类的全限定名，指向一个类型为CONSTANT_Class_info的类描述符常量，通过CONSTANT_Class_info中的索引值可以找到CONSTANT_Utf8_info类型的常量中的全限定名字符串。

# interfaces

interfaces接口索引集合，是一组u2类型的数据集合，描述类实现了哪些接口。

interfaces前面有个u2类型的interfaces_count，表示接口索引表容量。

# 字段表集合

field_info字段表，描述类或者接口中声明的变量，包括：静态变量、实例变量，不包括方法中的局部变量。

| 类型           | 名称             | 数量             |
| -------------- | ---------------- | ---------------- |
| u2             | access_flags     | 1                |
| u2             | name_index       | 1                |
| u2             | descriptor_index | 1                |
| u2             | attributes_count | 1                |
| attribute_info | attributes       | attributes_count |

## access_flags

字段修饰符，u2类型，如下

![字段修饰符](/JVM中的Class类文件结构/field_access_flags.png)

## name_index

name_index是对常量池项的引用，表示字段的简单名称。

## descriptor_index

descriptor_index是对常量池项的引用，表示字段和方法的描述符。

## attributes

属性表集合，存储一些额外信息，比如`final static int m = 123`可能会存在一项名为ConstantValue的属性，指向常量123。

字段表集合不会列出从父类或者父接口继承来的字段，但有可能会出现Java代码中不存在的字段，比如内部类中为了保持对外部类的访问性，编译器会自动添加指向外部类实例的字段。

# 方法表集合

method_info方法表集合：

| 类型           | 名称             | 数量             |
| -------------- | ---------------- | ---------------- |
| u2             | access_flags     | 1                |
| u2             | name_index       | 1                |
| u2             | descriptor_index | 1                |
| u2             | attributes_count | 1                |
| attribute_info | attributes       | attributes_count |

## access_flags

![method_access_flags](/JVM中的Class类文件结构/method_access_flags.png)

方法的代码经过编译器编译成字节码指令后，存放在方法属性表集合中的Code属性里。

如果父类方法在子类没有被重写，方法表中不会出现来自父类的方法信息。

可能会出现编译器自动添加的方法，比如类构造器`<clinit>`和实例构造器`<init>`。

# 属性表集合

属性表集合图：

![attribute_info-1](/JVM中的Class类文件结构/attribute_info_1.png)

![attribute_info-2](/JVM中的Class类文件结构/attribute_info_2.png)

属性表结构：

| 类型 | 名称                 | 数量             |
| ---- | -------------------- | ---------------- |
| u2   | attribute_name_index | 1                |
| u4   | attribute_length     | 1                |
| u1   | info                 | attribute_length |

- attribute_name_index，名称指向常量池中的一个CONSTANT_Utf8_info类型的常量。
- attribute_length，存储属性值占用的位数。

## Code属性

方法里面的代码经过编译器编译成字节码后，存储在Code属性里。Code属性出现在方法表的属性集合中，接口或者抽象类中的方法不存在Code属性。

Code属性结构：

| 类型           | 名称                   | 数量                   |
| -------------- | ---------------------- | ---------------------- |
| u2             | attribute_name_index   | 1                      |
| u4             | attribute_length       | 1                      |
| u2             | max_stack              | 1                      |
| u2             | max_locals             | 1                      |
| u4             | code_length            | 1                      |
| u1             | code                   | code_length            |
| u2             | exception_table_length | 1                      |
| exception_info | exception_table        | exception_table_length |
| u2             | attributes_count       | 1                      |
| attribute_info | attributes             | attributes_count       |

- attribute_name_index，指向CONSTANT_Utf8_info类型的常量索引，固定为“Code”，表示该属性的属性名称。
- attribute_length，属性值的长度
- max_stack，操作数栈深度的最大值
- max_locals，局部变量表需要的存储空间，单位是：变量槽Slot。byte、char、float、int、short、boolean、returnAddress占用一个变量槽，double和long需要两个变量槽。方法参数（包括this）、显式异常处理程序的参数（catch中定义的异常）、方法体中的局部变量等都需要依赖局部变量表来存放。
- code_length，字节码长度
- code，存储编译后生成的字节码

### 异常表集合

异常表属性结构：

| 类型 | 名称       | 数量 |
| ---- | ---------- | ---- |
| u2   | start_pc   | 1    |
| u2   | end_pc     | 1    |
| u2   | handler_pc | 1    |
| u2   | catch_type | 1    |

- start_pc，从第start_pc行开始
- end_pc，到第end_pc行，不包含end_pc行
- handler_pc，转到第handler_pc行继续处理
- catch_type，出现了catch_type或子类的异常，catch_type指向一个CONSTANT_Class_info类型的常量索引

## Exceptions属性

Exceptions，出现在方法表的属性集合中，用来列举出方法中可能抛出的受检查的异常，就是方法描述在throws后见列举的异常。

| 类型 | 名称                  | 数量                 |
| ---- | --------------------- | -------------------- |
| u2   | attribute_name_index  | 1                    |
| u4   | attribute_length      | 1                    |
| u2   | number_of_exceptions  | 1                    |
| u2   | exception_index_table | number_of_exceptions |

- number_of_exceptions，表示方法可能抛出number_of_exceptions个异常，每一个异常使用exception_index_table项表示
- exception_index_table，指向一个常量池中CONSTANT_Class_info型常量的索引

## LineNumberTable属性

LineNumberTable用于描述Java源码行号与字节码行号对应关系

| 类型             | 名称                     | 数量                     |
| ---------------- | ------------------------ | ------------------------ |
| u2               | attribute_name_index     | 1                        |
| u4               | attribute_length         | 1                        |
| u2               | line_number_table_length | 1                        |
| line_number_info | line_number_table        | line_number_table_length |

- line_number_info表中包含start_pc和line_number两个u2类型的数据项，start_pc是字节码行号，line_number是Java源码行号

## LocalVariableTable属性

LocalVariableTable用于描述栈帧中局部变量表的变量与Java源码中定义的变量间的关系。

| 类型                | 名称                        | 数量                        |
| ------------------- | --------------------------- | --------------------------- |
| u2                  | attribute_name_index        | 1                           |
| u4                  | attribute_length            | 1                           |
| u2                  | local_variable_table_length | 1                           |
| local_variable_info | local_variable_table        | local_variable_table_length |

- local_variable_info，代表了栈帧与源码中的局部变量的关联

### local_variable_info

| 类型 | 名称             | 数量 |
| ---- | ---------------- | ---- |
| u2   | start_pc         | 1    |
| u2   | length           | 1    |
| u2   | name_index       | 1    |
| u2   | descriptor_index | 1    |
| u2   | index            | 1    |

- start_pc，局部变量的生命周期开始的字节码偏移量
- length，局部变量生命周期作用范围覆盖长度
- name_index，指向常量池CONSTANT_Utf8_info类型常量索引，表示局部变量名称
- descriptor_index，指向常量池CONSTANT_Utf8_info类型常量索引，表示局部变量的描述符
- index，是局部变量在栈帧的局部变量表中变量槽的位置

## LocalVariableTypeTable

和LocalVariableTable相似，仅仅把记录的字段描述符的descriptor_index替换成了字段的特征签名Signature。

非泛型类型，描述符和特征签名描述的信息是一致的。泛型类型，描述符中的参数化类型被擦除掉，描述符不能准确描述泛型类型，就出现了LocalVariableTypeTable属性，使用字段的特征签名来完成泛型的描述

## SourceFile

SourceFile用于记录生成这个Class文件的源码文件名称。

| 类型 | 名称                 | 数量 |
| ---- | -------------------- | ---- |
| u2   | attribute_name_index | 1    |
| u4   | attribute_length     | 1    |
| u2   | sourcefile_index     | 1    |

- sourcefile_index，指向常量池中CONSTANT_Utf8_info类型常量索引，是源码文件名

## SourceDebugExtension

SourceDebugExtension，用来存储额外的代码调试信息。

| 类型 | 名称                              | 数量 |
| ---- | --------------------------------- | ---- |
| u2   | attribute_name_index              | 1    |
| u4   | attribute_length                  | 1    |
| u1   | debug_extension[attribute_length] | 1    |

- debug_extension，存储的是额外的调试信息，通过一组变长UTF-8格式来表示的字符串，一个类最多只有一个SourceDebugExtension属性。

## ConstantValue属性

ConstantValue用来通知虚拟机自动为静态变量赋值，static修饰的变量可以使用这个属性。

实例变量赋值是在实例构造器`<init>`中进行，类变量有两种方式：在类构造器`<clinit>`方法中或者使用ConstantValue属性。

Oracle的编译器是：如果是final static修饰，也就是一个常量，并且类型是基本类型或者是String类型，就会使用ConstantValue来进行初始化；如果没有被final修饰，或者类型非基本类型以及字符串，会选择在`<clinit>`方法中进行初始化。

| 类型 | 名称                 | 数量 |
| ---- | -------------------- | ---- |
| u2   | attribute_name_index | 1    |
| u4   | attribute_length     | 1    |
| u2   | constantvalue_index  | 1    |

- attribute_length，固定为2
- constant_value，表示常量池中个一个字面量的引用，根据字段类型不同，字面量可以是：CONSTANT_Long_info、CONSTANT_Float_info、CONSTANT_Double_info、CONSTANT_Integer_info、CONSTANT_String_info

## InnerCalsses属性

InnerClasses属性记录内部类和宿主类之间的关联，属性结构：

| 类型               | 名称                 | 数量              |
| ------------------ | -------------------- | ----------------- |
| u2                 | attribute_name_index | 1                 |
| u4                 | attribute_length     | 1                 |
| u2                 | number_of_classes    | 1                 |
| inner_classes_info | inner_classes        | number_of_classes |

inner_classes_info代表每个内部类信息

### inner_classes_info

| 类型 | 名称                     | 数量 |
| ---- | ------------------------ | ---- |
| u2   | inner_class_info_index   | 1    |
| u2   | outer_class_info_index   | 1    |
| u2   | inner_name_index         | 1    |
| u2   | inner_class_access_flags | 1    |

- inner_class_info_index，指向常量池CONSTANT_Class_info类型常量索引，代表内部类的符号引用
- outer_class_info_index，指向常量池CONSTANT_Class_info类型常量索引，代表外部类的符号引用
- inner_name_index，指向常量池CONSTANT_Utf8_info类型常量索引，代表内部类名称，匿名内部类为0
- inner_class_access_flags，内部类的访问标志

![inner_class_access_flags](/JVM中的Class类文件结构/inner_class_access_flags.png)

## Deprecated属性

Deprecated是布尔类型属性，表示某个类、字段、方法已经被标记为不推荐使用

| 类型 | 名称                 | 数量 |
| ---- | -------------------- | ---- |
| u2   | attribute_name_index | 1    |
| u4   | attribute_length     | 1    |

- attribute_length为0x00000000，因为没有任何属性值需要设置

## Synthetic属性

Synthetic属性表示字段或方法不是由Java源码直接产生，而是编译器自行添加的。最典型的例子就是枚举类中自动生成的枚举元素数组和嵌套类的桥接方法。

| 类型 | 名称                 | 数量 |
| ---- | -------------------- | ---- |
| u2   | attribute_name_index | 1    |
| u4   | attribute_length     | 1    |

- attribute_length为0x00000000，因为没有任何属性值需要设置

## Signature属性

Signature在JDK5中增加，是一个可选定长属性，用于类、字段表、方法表结构的属性表中。用来记录泛型签名信息，Java泛型采用擦除法实现伪泛型，字节码Code属性中没有泛型信息。

| 类型 | 名称                 | 数量 |
| ---- | -------------------- | ---- |
| u2   | attribute_name_index | 1    |
| u4   | attribute_length     | 1    |
| u2   | signature_index      | 1    |

- signature_index必须是对一个常量池的有效索引，并且该索引是CONSTANT_Utf8_info结构，表示类签名或方法类型签名或字段类型签名。

## MethodParameters属性

MethodParameters在JDK8新加入的，用在方法表中的变长属性，记录方法的各个形参名称和信息。

| 类型      | 名称                 | 数量             |
| --------- | -------------------- | ---------------- |
| u2        | attribute_name_index | 1                |
| u4        | attribute_length     | 1                |
| u1        | parameters_count     | 1                |
| parameter | parameters           | parameters_count |

parameter属性结构：

| 类型 | 名称         | 数量 |
| ---- | ------------ | ---- |
| u2   | name_index   | 1    |
| u4   | access_flags | 1    |

- name_index指向常量池CONSTANT_Utf8_info类型的索引值，表示参数名称。
- access_flags，参数的状态，包含：ACC_FIANL被final修饰、ACC_SYNTHETIC编译器自动生成、ACC_MANDATED表示该参数是在源文件中隐式定义，比如this关键字。

## 运行时注解属性

JDK1.5：RuntimeVisibleAnnotations、RuntimeInvisibleAnnotations、RuntimeVisibleParameterAnnotations、RuntimeInvisibleParameterAnnotations用来存储源码中注解信息

JDK1.8：RuntimeVisibleTypeAnnotations、RuntimeInvisibleTypeAnnotations，用来存储源码中类型注解信息