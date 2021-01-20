---
title: Java父类引用指向子类对象（向上转型）
date: 2020-07-12 15:44:11
tags:
	- Java
categories:
	- 编程相关
	- Java
	- Java基础
fileName: java_polymorphic
---

参考：https://www.cnblogs.com/ChrisMurphy/p/5054256.html

父类的引用指向子类的对象是Java中多态的重要表现形式

#### 表现形式

父类为Animal，子类为Cat，如下即为父类引用指向子类对象的形式，**声明的是父类，实际指向的是子类的一个对象**。（其中Animal可以是类也可以是接口，Cat是继承或实现Animal的子类）

```
Animal animal = new Cat()
```

这种情况也即为**向上转型**，父类引用指向子类实例。**可以调用子类重现父类的方法以及父类派生的方法，无法调用子类独有的方法**。



#### 意义

子类是对父类的一个改进和扩充，所以一般子类在功能上较父类更强大，属性较父类更独特， 定义一个父类类型的引用指向一个子类的对象既可以**使用子类强大的功能**，又可以**抽取父类的共性**。 所以，父类类型的引用可以调用父类中定义的所有属性和方法，而对于子类中定义而父类中没有的方法，父类引用是无法调用的



#### 示例

父类

```
public class Father {
    // 父类中的func1方法
    // 由于子类中对该方法只是进行了重载而没有进行重写
    // 因此该方法仍然有效
    public void func1() {
        System.out.println("Father func1");
    }

    // 父类中的func2方法
    // 由于在子类中重写了该方法
    // 所以在“向上转型”中，该方法不再有效
    // 取而代之的是子类中的fun2方法
    public void func2() {
        System.out.println("Father func2");
    }
}
```

子类

```
public class Child extends Father {
    // 对func1方法的一个重载
    // 由于在父类中没有定义该方法，所以该方法不能被父类的引用调用
    // 如果进行调用即会报错
    public void func1(int i) {
        System.out.println("Child func1");
    }

    // 对父类方法的重写
    // 能够起作用
    public void func2() {
        System.out.println("Child func2");
    }
}
```

主函数

```
public class Main {
    public static void main(String[] args) {
	// write your code here
        Father test = new Child();
        test.func1();   // Father func1
        test.func2();   // Child func2
        test.func1(10); // 报错
    }
}
```

上述示例中，子类Child继承了父类Father，并重载了父类的func1()方法，重写了父类的func2()方法。重载后的func1(int i)和func1()不再是同一个方法，由于父类中没有func1(int i)，那么，父类类型的引用child就不能调用func1(int i)方法。而子类重写了func2()方法，那么父类类型的引用child在调用该方法时将会调用子类中重写的func2()。 



#### 总结

1、**使用父类类型的引用指向子类的对象；** 

2、**该引用只能调用父类中定义的方法和变量；** 

3、**如果子类中重写了父类中的一个方法，那么在调用这个方法的时候，将会调用子类中的这个方法；（动态连接、动态调用）** 

4、**变量不能被重写（覆盖），”重写“的概念只针对方法，如果在子类中”重写“了父类中的变量，那么在编译时会报错。**





