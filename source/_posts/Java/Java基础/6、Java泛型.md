---
title: 6、Java泛型
date: 2020-07-28 11:40:48
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: Java-generic
---

### 泛型

#### 概念

泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。

Java的泛型是**伪泛型**，这是因为Java在编译期间，所有的泛型信息都会被擦掉

Java的泛型基本上都是在编译器这个层次上实现的，在生成的字节码中是不包含泛型中的类型信息的，使用泛型的时候加上类型参数，在编译器编译的时候会去掉，这个过程成为**类型擦除**。

如在代码中定义`List<Object>`和`List<String>`等类型，在编译后都会变成`List`，JVM看到的只是List，而由泛型附加的类型信息对JVM是看不到的。Java编译器会在编译时尽可能的发现可能出错的地方，但是仍然无法在运行时刻出现的类型转换异常的情况，类型擦除也是Java的泛型与C++模板机制实现方式之间的重要区别。

#### 使用方式

注：变量声明的类型必须匹配传递给实际对象的类型（即前后尖括号中的内容必须相同）

**1、泛型类**

```
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{ 
   
    private T key;

    public Generic(T key) { 
        this.key = key;
    }

    public T getKey(){ 
        return key;
    }
}
```



**2、泛型接口**

```
public interface Generator<T> {
    public T method();
}
```

实现泛型接口时不指定类型

```
class GeneratorImpl<T> implements Generator<T>{
    @Override
    public T method() {
        return null;
    }
}
```

实现泛型接口时指定类型

```\
class GeneratorImpl<T> implements Generator<String>{
    @Override
    public String method() {
        return "hello";
    }
}
```



**3、泛型方法**

注：泛型方法不一定要写在泛型类当中

```
public static <E> void printArray( E[] inputArray )
{         
	for ( E element : inputArray ){        
    	System.out.printf( "%s ", element );
    }
        System.out.println();
}
```





#### 通配符

**常用的通配符为： T，E，K，V，？**

本质上这些个都是通配符，没啥区别，只不过是编码时的一种约定俗成的东西。比如上述代码中的 T ，我们可以换成 A-Z 之间的任何一个 字母都可以，并不会影响程序的正常运行，但是如果换成其他的字母代替 T ，在可读性上可能会弱一些。**通常情况下，T，E，K，V，？ 是这样约定的：**

- ？ 表示不确定的 java 类型
- T (type) 表示具体的一个java类型
- K V (key value) 分别代表java键值中的Key Value
- E (element) 代表Element

**上界通配符 <? extends 类名> **：表示参数化的类型可能是所指定的类型，或者是此类型的**子类**。

**下界通配符 < ? super 类名>**：表示参数化的类型可能是所指定的类型，或者是此类型的父类型，直至 Object





参考：

类型擦除：https://www.cnblogs.com/wuqinglong/p/9456193.html

https://juejin.im/post/5d5789d26fb9a06ad0056bd9