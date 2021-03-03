---
title: Java：泛型
date: 2020-07-28 11:40:48
tags:
	- Java
categories:
	- 编程相关
	- Java
	- Java基础
fileName: Java-generic
---

# 泛型基础

## 基本概念

泛型的本质是**参数化类型**，也就是说所操作的数据类型被指定为一个参数。

Java的泛型是**伪泛型**，这是因为Java在编译期间，所有的泛型信息都会被擦掉

Java的泛型基本上都是在编译器这个层次上实现的，在生成的字节码中是不包含泛型中的类型信息的，使用泛型的时候加上类型参数，在编译器编译的时候会去掉，这个过程成为**类型擦除**。

如在代码中定义`List<Object>`和`List<String>`等类型，在编译后都会变成`List`，JVM看到的只是List，而由泛型附加的类型信息对JVM是看不到的。Java编译器会在编译时尽可能的发现可能出错的地方，但是仍然无法在运行时刻出现的类型转换异常的情况，类型擦除也是Java的泛型与C++模板机制实现方式之间的重要区别。

## 三种主要使用形式

引入类型可以用来修饰成员变量/局部变量/参数/返回值

注：变量声明的类型必须匹配传递给实际对象的类型（即前后尖括号中的内容必须相同）

### 1、泛型类

> 常见的如`ArrayList`、`HashSet`、`HashMap`等等

```
// 此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
// 在实例化泛型类时，必须指定T的具体类型
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



### 2、泛型接口

> 实现泛型的接口，如`Iterator`、`List`等即为泛型接口

**泛型接口定义**

```
public interface Generator<T> {
    public T method();
}
```

#### 实现时不指定类型

```
class GeneratorImpl<T> implements Generator<T>{
    @Override
    public T method() {
        return null;
    }
}
```

#### 实现时指定类型

```\
class GeneratorImpl<T> implements Generator<String>{
    @Override
    public String method() {
        return "hello";
    }
}
```



### 3、泛型方法

> 具有泛型参数的方法，可位于普通类/泛型类中（**泛型方法不一定要写在泛型类当中**）
>
> 如`Arrays.sort()`等方法即是泛型方法
>
> 泛型参数\<T\>在修饰符后，返回类型前，如下示例
>
> 若传入参数不统一会寻找共同的*超类*，一般应该统一参数

#### 泛型方法定义

```
public static <E> void printArray( E[] inputArray )
{         
	for ( E element : inputArray ){        
    	System.out.printf( "%s ", element );
    }
        System.out.println();
}
```

#### 泛型方法调用

```
Target t = new Target();
String s = t.<String>method();	// 调用时不省略泛型
String s = t.method();	// 调用时省略泛型
```



# 泛型限定

## 泛型通配符

### 常用通配符

> **常用的有： T，E，K，V，？**（通常只将?视为通配符）
>
> 本质上这些个都是通配符，没啥区别，只不过是编码时的一种约定俗成的东西。比如上述代码中的 T ，我们可以换成 A-Z 之间的任何一个 字母都可以，并不会影响程序的正常运行，但是如果换成其他的字母代替 T ，在可读性上可能会弱一些。

- ？ 表示不确定的 Java类型
- T (Template) 表示具体的一个Java类型，自定义泛型常用T
- K V (Key Value) 分别代表Java键值中的Key Value
- E (Element) 代表元素Element

### 上界通配符

`<? extends 类名> `：表示能接受的参数类型可能是*所指定的类型本身，或者是此类型的子类*

> 使用上界通配符，编译器能够保证取出来的类型一定是指定类型的子类，但不保证放入的对象是什么类型
>
> 所以**只能进行`get`操作，而不能进行`set`操作**

举例说明：

```
Pair<Apple> apples = new Pair<>(new Apple(3));
Pair<? extends Fruit> fruit = apples;
fruit.set(new Orange(3));	// 编译错误
```

### 下界通配符

`< ? super 类名>`：表示参数化的类型可能是*所指定的类型本身，或者是此类型的父类型，直至 Object*

> 编译器能保证放入的是其本身或其超类（传入子类时进行类型转换），但无法保证取出的是哪个类。
>
> 所以**只能进行`set`操作，而不能进行`get`操作**

### 泛型PECS原则

> Producer Extends 泛型是生产者，向外输出，使用`Extends`

> Consumer Super 泛型是消费者，向内增加东西，使用`Super`

### ？和T的区别

> `T`是一个**确定**的类型，而`?`是一个不确定的类型
>
> 可以对`T`进行操作，而不能对`?`进行操作



### extends关键字

> 使用`extends`关键字约定该类型必须是某某的*子类*

如`<T extends Comparable>`约定T必须是`Comparable`的子类

`extends`后可跟多个，**以`&`拼接**，同时该限定可以有多个接口，但只能有一个类，且类必须排第一位。

### 泛型的类型关系

> Java中的泛型是不变的，即List\<Object\>与List\<String\>并无父类与子类的关系
>
> 



# 泛型实现的本质

## 类型擦除

泛型是JDK1.5引入的新特性，因此JVM中没有泛型对象，而是采用**类型擦除**技术，只有普通的类和方法

> 擦除泛型对象，替换为原始类型（无限定则替换为为Object，有限定则替换为第一个类型），使得该类与引入泛型之前的类并无不同

比如：`Pair<T>`则可视为`Pair<Object>`。因为T未指定类型

`Pair<T extends A & B>`则可视为`Pair<A>`

## 自动类型转换

> 擦除泛型后，编译时会进行自动类型转换
>
> 即先生成一个`Object`对象，再使用强制类型转化转换为对应的对象



## 自动桥方法

> 即泛型中自动生成的参数为`Object`方法会自动调用用户传入相关参数类型的对应方法





> 参考：
>
> mooc华东师范大学：Java高级
>
> 类型擦除：https://www.cnblogs.com/wuqinglong/p/9456193.html
>
> https://juejin.im/post/5d5789d26fb9a06ad0056bd9

