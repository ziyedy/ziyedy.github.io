---
title: Java类加载机制
date: 2021-02-04 13:38:33
tags:
	- JVM
categories:
	- Java
	- JVM
fileName: jvm-classloader
---

# 类加载的时机

## 类的生命周期

> 类从被加载到虚拟机内存开始，到卸载出内存为止，它的整个生命周期包括以下 7 个阶段

<img src="http://cdn.ziyedy.top/JVM%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%9C%BA%E5%88%B6/%E7%B1%BB%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png" style="zoom:67%;" />

## 类加载中初始化的时机

Java 虚拟机规范没有强制约束类加载过程的第一阶段（即：加载）什么时候开始，但**对于“初始化”阶段，有着严格的规定**。有且仅有 5 种情况必须立即对类进行“初始化”（即**主动引用**）

* 在遇到 new、putstatic、getstatic、invokestatic **特定字节码指令**时，如果类尚未初始化，则需要先触发其初始化

* 对类进行**反射调用**时，如果类还没有初始化，则需要先触发其初始化
* 初始化一个类时，如果其父类还没有初始化，则需要先初始化父类（*与接口的区别*）
* 虚拟机启动时，用于需要指定一个**包含 `main()` 方法的主类**，虚拟机会先初始化这个主类
* 当使用 JDK 1.7 的动态语言支持时，如果一个 java.lang.invoke.MethodHandle 实例最后的解析结果为 REF_getStatic、REF_putStatic、REF_invokeStatic 的方法句柄，并且这个方法句柄所对应的类还没初始化，则需要先触发其初始化

## 接口的加载

* 当一个类在初始化时，要求其父类全部都已经初始化过了

* 一个接口在初始化时，并不要求其父接口全部都完成了初始化，当**真正用到父接口的时候才会初始化**。



# 类加载过程

## 加载

### 加载的过程

1. 通过类的全限定名获取该类的二进制字节流
2. 将二进制字节流所代表的静态结构转化为方法区的运行时数据结构
3. 在内存中创建一个代表该类的 `java.lang.Class` 对象，作为方法区这个类的各种数据的访问入口

*注：对于 HotSpot 虚拟机而言，Class 对象比较特殊，它虽然是对象，但存放在方法区中*

### “非数组类”与“数组类”加载比较

* 非数组类加载阶段可以使用系统提供的引导类加载器，也可以由用户自定义的类加载器完成（如重写一个类加载器的 `loadClass()` 方法）
* **数组类本身不通过类加载器创建**，它是由 Java 虚拟机直接创建的，再由类加载器创建数组中的元素类

## 连接

> 就是将已经读入内存的类的二进制数据合并到JVM运行时环境中

### 验证

> 确保被加载类的正确性（即class文件的字节流符合一般文件格式及当前虚拟机的要求）

* **类文件结构检查**∶按照JVM规范规定的类文件结构进行

* **元数据验证**：对字节码描述的信息进行语义分析，保证其符合Java语言规范要求

* **字节码验证**∶通过对数据流和控制流进行分析，确保程序语义是合法和符合逻辑的。这里主要对**方法体**进行校验

* **符号引用验证**：对类自身以外的信息，也就是常量池中的各种符号引用，进行匹配校验，**确保解析正常执行**

### 准备

> 为类的静态成员变量（static修饰）分配内存，并设置初始值

**这些变量（不包括实例变量）所使用的内存都在方法区中进行分配**

```java
public static int value = 123;			// 准备阶段后初始值为 0
public static final int value = 123;	// 准备阶段后初始值为 123
```

### 解析

> 将常量池中的符号引用替换为直接引用

* 符号引用：以一组无歧义的符号来描述所引用的目标，与虚拟机无关

* 直接引用：直接指向目标的指针、相对偏移量、或是能间接定位到目标的句柄，是和虚拟机实现相关的

主要针对：类、接口、字段、类方法、接口方法、方法类型、方法句柄、调用点限定符

## 初始化

> 为类的静态变量赋初始值，或者说是执行类构造器`<clinit>`方法的过程

> `<clinit>`方法是由**编译器自动收集**类中的所有类变量的赋值动作和静态语句块（static {} 块）中的语句**合并**产生的，编译器收集的顺序是由语句在源文件中出现的顺序所决定的



# 类加载器

## 类加载器种类

Java虚拟机自带的加载器包括如下3种∶

* 启动类加载器（BootstrapClassLoader）：由C++语言实现，是虚拟机的一部分

   负责将存放在 `<JAVA_HOME>\lib` 目录中的，并且能被虚拟机识别的（仅**按照文件名**识别，如 rt.jar，名字不符合的类库即使放在 lib 目录中也不会被加载）类库加载到虚拟机内存中

* 拓展类加载器（Extension ClassLoader，JDK8）  

   JDK9之后为平台类加载器（PlatformClassLoader）。负责加载 `<JAVA_HOME>\lib\ext` 目录中的所有类库，开发者可以直接使用扩展类加载器

* 应用程序类加载器（AppClassLoader）

  它负责加载用户类路径（classpath）上所指定的类库，开发者可以直接使用这个类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中**默认的类加载器**。

* 用户自定义的加载器

  是`java.lang.ClassLoader`的子类，用户可以定制类的加载方式;只不过自定义类加载器其加载的顺序是在所有系统类加载器的最后

<img src="http://cdn.ziyedy.top/JVM%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%9C%BA%E5%88%B6/%E7%B1%BB%E5%8A%A0%E8%BD%BD%E5%99%A8.png" style="zoom:67%;" />



```java
public class Test {

    public static void main(String[] args) {
        Test t = new Test();

        Class<? extends Test> tClass = t.getClass();

        ClassLoader classLoader = tClass.getClassLoader();
        System.out.println(classLoader);
        System.out.println(classLoader.getParent());
        System.out.println(classLoader.getParent().getParent());
    }
}

/**
 * jdk.internal.loader.ClassLoaders$AppClassLoader@3fee733d
 * jdk.internal.loader.ClassLoaders$PlatformClassLoader@6acbcfc0
 * null	表示启动类加载器
*/
```

## 双亲委派模型

JVM中的ClassLoader通常采用双亲委派模型，要求除了启动类加载器外，其余的类加载器都应该有自己的父级加载器（这里的父子关系一般不会以继承的关系实现，而是**以组合关系来复用父加载器的代码**）

<img src="http://cdn.ziyedy.top/JVM%E7%B1%BB%E5%8A%A0%E8%BD%BD%E6%9C%BA%E5%88%B6/%E5%8F%8C%E4%BA%B2%E5%A7%94%E6%B4%BE%E6%9C%BA%E5%88%B6.png" style="zoom: 80%;" />

### 工作过程
1、首先判断当前类是否被加载过，已经被加载的类会直接返回，否则才会尝试加载；

2、加载的时候，首先会把该请求委派该父类加载器的 `loadClass()` 处理，因此所有的请求最终都应该传送到顶层的启动类加载器 `BootstrapClassLoader` 中；

3、当父类加载器无法完成这个加载请求（找不到所需的类）时，子加载器才会尝试自己去加载。

### 为何使用双亲委派模型

1、双亲委派模型保证了Java程序的稳定运作，可以避免类的重复加载（JVM 区分不同类的方式不仅仅根据类名，相同的类文件被不同的类加载器加载产生的是两个不同的类），*保证没有同包同名的类*

2、保证了Java的核心API不被篡改，*保证系统类不会被覆盖*

比如我们编写一个称为 `java.lang.Object` 类的话，那么程序运行的时候，系统就会出现多个不同的 `Object` 类，而双亲委派机制很好的避免了这个问题。

## 自定义类加载器

> 由于实现双亲委派的代码在`java.lang.ClassLoader`的`loadClass()`方法中，如果自定义类加载器的话，推荐覆盖实现`findClass()`方法

```java
public class MyClassLoader extends ClassLoader{
    private String name;
    public MyClassLoader(String name) {
        this.name = name;
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        byte[] data = loadClassData(name);
        return this.defineClass(name, data, 0, data.length);
    }

    private byte[] loadClassData(String clsName) {
        byte[] data = null;
        // 进行输入输出流的编写
        // 可以自行定义一些规则，比如加载某些或不加载某些
        return data;
    }
}
```

### 自定义类加载器的应用场景

参考：https://www.zhihu.com/question/46719811

* 依赖冲突
* 热加载
* 热部署
* 加密保护：正向加密后，在加载class文件之前通过自定义classloader先进行反向的解密操作



参考：

> https://snailclimb.gitee.io/javaguide/#/docs/java/jvm/%E7%B1%BB%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B
>
> 《深入理解Java虚拟机》
>
> https://github.com/doocs/jvm





