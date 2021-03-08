---
title: Java字节码
date: 2021-02-02 23:07:03
tags:
	- JVM
categories:
	- Java
	- JVM
fileName: jvm-classbytecode
---

## Class文件

### 概念

> .class文件即字节码（bytecode）文件，是一个二进制文件
>
> 具有平台无关性，可以由其他语言编译生成，由JVM执行

### 文件构成

主要包括两种数据类型：

1. 定长数据：无符号数，u1, u2, u4（分别代表1个字节、2个字节、4个字节的无符号数），所以u1相当于就是使用两个16进制位表示
2. 不定长数据：由多个无符号数组成，通常在数据前面给出其长度

### 文件内容

每个`.class`文件对应一个`ClassFile`结构（摘自Java虚拟机规范）

<img src="http://cdn.ziyedy.top/1%E3%80%81Java%E5%AD%97%E8%8A%82%E7%A0%81/classfile.png" style="zoom:80%;" />

对应到中文按照顺序分别为

* Magic（魔数）：唯一作用是确定该文件是否为一个能被JVM接受的class文件，其固定值为0xCAFEBABE
* minor_version（副版本号）、major_version（主版本号）：可以理解为标识JDK的版本
* constant_pool_count（常量池计数器）：其值等于常量池中成员+1
* constant_pool[]（常量池）：一种表结构，按顺序存储相关结构，具体结构可进行查找

> 常量池主要存放两类常量：
>
> 字面量（如文本字符串、 final 的常量值等）
>
> 符号引用（类和接口的全限定名、字段的名称和描述符、方法的名称和描述符）

* access_flag（访问标志）：用于表示某个类或接口的访问权限及属性（是类还是接口，是否被public等修饰）

![访问标志表](http://cdn.ziyedy.top/1%E3%80%81Java%E5%AD%97%E8%8A%82%E7%A0%81/%E8%AE%BF%E9%97%AE%E6%A0%87%E5%BF%97%E8%A1%A8.png)

* this_class（类索引）：表示这个class文件所定义的类或接口，由常量池中某项表示
* super_class（父类索引）：为0表示没有父类（即Object类），否则与类索引相同，只是表示其父类
* interfaces_count（接口计数器）：表示当前接口或类实现接口的数量
* interfaces[]（接口表）：存储接口
* fields_count（字段计数器）、fields[]（字段表）
* methods_count（方法计数器）、methods[]（方法表）
* attributes_count（属性计数器）、attributes[]（属性表）：字节码最后一部分，存放了基本信息，由编译器写入，JVM会忽略不认识的属性信息。



# Class文件、反编译文件、源码的对照阅读

## Java源代码

定义在`HelloWorld.java`中的一个简单代码

```
package com.jvm.classfile;

public class HelloWorld {
    private static String msg = "Hello World";

    public static void main(String[] args) {
        System.out.println("msg = " + msg);
    }
}
```

## Class字节码文件

### 魔数、版本号

如下所示，魔数固定为CAFEBABE，而主版本号16进制39转化为10进制就是57，代表Java13（版本对应可查看《Java虚拟机规范》）

<img src="http://cdn.ziyedy.top/1%E3%80%81Java%E5%AD%97%E8%8A%82%E7%A0%81%E9%AD%94%E6%95%B0%E3%80%81%E4%B8%BB%E7%89%88%E6%9C%AC%E5%8F%B7.png" style="zoom:80%;" />

### 常量池

常量池记录中具有以下通用格式：

```
cp_info {
	u1 tag;		// 用于表示常量类型
	u1 info[];	// 后续的内容由tag确定的类型确定
}
```

如下，常量个数3A即为58，而根据下面反编译文件，符合57+1

<img src="http://cdn.ziyedy.top/1%E3%80%81Java%E5%AD%97%E8%8A%82%E7%A0%81/%E5%B8%B8%E9%87%8F.png" style="zoom:80%;" />

而后第一个常量tag为0A（即10），根据对应表可见为如下常量类型，而后两个参数分别为2和3，与反编译文件中常量池第一个常量一致。

![](http://cdn.ziyedy.top/1%E3%80%81Java%E5%AD%97%E8%8A%82%E7%A0%81/methodref.png)

### 访问标志及各类索引

如下，首先根据最后一个常量为Lookup找到常量池结束的位置，类标识为0021（即0x0001和0x0020），对照反编译文件符合。

而类索引与父类索引对应反编译文件第9、10行也一致

![](http://cdn.ziyedy.top/1%E3%80%81Java%E5%AD%97%E8%8A%82%E7%A0%81/%E7%B1%BB%E6%A0%87%E8%AF%86.png)

### 接口

由于源码中未实现接口，因此如上节中父类标识后为0000，表示不存在接口

### 字段

字段的通用表示格式：

```
field_info {
	u2 access_flags;		// 字段访问权限标识符
	u2 name_index;			// 对常量池的索引，即字段名
	u2 descriptor_index;	// 对常量池的索引，字段类型
	u2 attributes_count;	// 附加属性数量
	attribute_info[]
}
```

如下，0001表示只有一个字段，000A表示源码中的`private static`，对应关系可查对应表

0011表示字段名索引为17，由反编译文件第29行可知，对应的为`msg`，与源码定义一致

0012表示字段类型索引为18，对应反编译文件第30行，为`String`类型

0000表示无附加属性

![](http://cdn.ziyedy.top/1%E3%80%81Java%E5%AD%97%E8%8A%82%E7%A0%81/%E5%AD%97%E6%AE%B5.png)

### 方法

方法的通用表示格式：

```
method_info {
	u2 access_flags;		// 访问权限
	u2 name_index;			// 名称索引
	u2 descriptor_index;	// 方法描述符
	u2 attribute_count;
	attribute_info[];
}
```

由上一节向后看：

0003表示该类有三个方法，类初始化方法、实例初始化方法、源码中我们自己定义的方法

其余的查看方法与字段相同，可以看到分别对应相关的方法，以及其参数与返回值。



## 反编译文件

### 反编译结果

使用`javap -verbose`反编译后的文件，如下。

可以看到其中内容与之间说明的文件内容是**一一对应**的，如`Constant pool`中就包括常量池的内容

```
Classfile /D:/java-demo/jvm/out/production/jvm/com/jvm/classfile/HelloWorld.class
  Last modified 2021年2月3日; size 1128 bytes
  SHA-256 checksum ce04ce48aa10126385c2a8b9bd8399ebf073b33cd6c7cb17f4b3ec73903aef7a
  Compiled from "HelloWorld.java"
public class com.jvm.classfile.HelloWorld
  minor version: 0
  major version: 57
  flags: (0x0021) ACC_PUBLIC, ACC_SUPER
  this_class: #14                         // com/jvm/classfile/HelloWorld
  super_class: #2                         // java/lang/Object
  interfaces: 0, fields: 1, methods: 3, attributes: 3
Constant pool:
   #1 = Methodref          #2.#3          // java/lang/Object."<init>":()V
   #2 = Class              #4             // java/lang/Object
   #3 = NameAndType        #5:#6          // "<init>":()V
   #4 = Utf8               java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = Fieldref           #8.#9          // java/lang/System.out:Ljava/io/PrintStream;
   #8 = Class              #10            // java/lang/System
   #9 = NameAndType        #11:#12        // out:Ljava/io/PrintStream;
  #10 = Utf8               java/lang/System
  #11 = Utf8               out
  #12 = Utf8               Ljava/io/PrintStream;
  #13 = Fieldref           #14.#15        // com/jvm/classfile/HelloWorld.msg:Ljava/lang/String;
  #14 = Class              #16            // com/jvm/classfile/HelloWorld
  #15 = NameAndType        #17:#18        // msg:Ljava/lang/String;
  #16 = Utf8               com/jvm/classfile/HelloWorld
  #17 = Utf8               msg
  #18 = Utf8               Ljava/lang/String;
  #19 = InvokeDynamic      #0:#20         // #0:makeConcatWithConstants:(Ljava/lang/String;)Ljava/lang/String;
  #20 = NameAndType        #21:#22        // makeConcatWithConstants:(Ljava/lang/String;)Ljava/lang/String;
  #21 = Utf8               makeConcatWithConstants
  #22 = Utf8               (Ljava/lang/String;)Ljava/lang/String;
  #23 = Methodref          #24.#25        // java/io/PrintStream.println:(Ljava/lang/String;)V
  #24 = Class              #26            // java/io/PrintStream
  #25 = NameAndType        #27:#28        // println:(Ljava/lang/String;)V
  #26 = Utf8               java/io/PrintStream
  #27 = Utf8               println
  #28 = Utf8               (Ljava/lang/String;)V
  #29 = String             #30            // Hello World
  #30 = Utf8               Hello World
  #31 = Utf8               Code
  #32 = Utf8               LineNumberTable
  #33 = Utf8               LocalVariableTable
  #34 = Utf8               this
  #35 = Utf8               Lcom/jvm/classfile/HelloWorld;
  #36 = Utf8               main
  #37 = Utf8               ([Ljava/lang/String;)V
  #38 = Utf8               args
  #39 = Utf8               [Ljava/lang/String;
  #40 = Utf8               <clinit>
  #41 = Utf8               SourceFile
  #42 = Utf8               HelloWorld.java
  #43 = Utf8               BootstrapMethods
  #44 = MethodHandle       6:#45          // REF_invokeStatic java/lang/invoke/StringConcatFactory.makeConcatWithConstants:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Lja
va/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
  #45 = Methodref          #46.#47        // java/lang/invoke/StringConcatFactory.makeConcatWithConstants:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/Me
thodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
  #46 = Class              #48            // java/lang/invoke/StringConcatFactory
  #47 = NameAndType        #21:#49        // makeConcatWithConstants:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lan
g/Object;)Ljava/lang/invoke/CallSite;
  #48 = Utf8               java/lang/invoke/StringConcatFactory
  #49 = Utf8               (Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
  #50 = String             #51            // msg = \u0001
  #51 = Utf8               msg = \u0001
  #52 = Utf8               InnerClasses
  #53 = Class              #54            // java/lang/invoke/MethodHandles$Lookup
  #54 = Utf8               java/lang/invoke/MethodHandles$Lookup
  #55 = Class              #56            // java/lang/invoke/MethodHandles
  #56 = Utf8               java/lang/invoke/MethodHandles
  #57 = Utf8               Lookup
{
  public com.jvm.classfile.HelloWorld();
    descriptor: ()V
    flags: (0x0001) ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 3: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   Lcom/jvm/classfile/HelloWorld;

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: getstatic     #13                 // Field msg:Ljava/lang/String;
         6: invokedynamic #19,  0             // InvokeDynamic #0:makeConcatWithConstants:(Ljava/lang/String;)Ljava/lang/String;
        11: invokevirtual #23                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        14: return
      LineNumberTable:
        line 7: 0
        line 8: 14
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      15     0  args   [Ljava/lang/String;

  static {};
    descriptor: ()V
    flags: (0x0008) ACC_STATIC
    Code:
      stack=1, locals=0, args_size=0
         0: ldc           #29                 // String Hello World
         2: putstatic     #13                 // Field msg:Ljava/lang/String;
         5: return
      LineNumberTable:
        line 4: 0
}
SourceFile: "HelloWorld.java"
BootstrapMethods:
  0: #44 REF_invokeStatic java/lang/invoke/StringConcatFactory.makeConcatWithConstants:(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang
/String;[Ljava/lang/Object;)Ljava/lang/invoke/CallSite;
    Method arguments:
      #50 msg = \u0001
InnerClasses:
  public static final #57= #53 of #55;    // Lookup=class java/lang/invoke/MethodHandles$Lookup of class java/lang/invoke/MethodHandles
```

### 反编译方法阅读

stack：方法执行时操作栈深度

locals：局部变量所需的存储空间，单位是slot（虚拟机为局部变量分配内存的最小单位）
