---
title: Java：接口与抽象类
date: 2020-07-28 11:38:34
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: java-interface-abstractclass
---







abstract关键字

抽象类，无法进行实例化的类（可以通过向上转型指向子类实例 ）

抽象方法：子类必须进行实现（或者子类也设置为抽象类），在父类定义时不能有方法体



包含抽象方法的类是抽象类

抽象类中可以没有抽象方法

static/final/private不能与abstract共存



接口：命名通常以“I”打头

接口定义了某一批类所需要遵守的规范

接口不关心这些类的内部数据，也不关心类中方法的实现细节，只规定这些类中必须提供某些方法





接口访问修饰符：public 默认

接口中抽象方法可以不写abstract关键字

作为接口的实现类必须实现接口中定义的所有方法，否则需要将该类设置为抽象类

接口中可以包含常量，默认public static final

```
public interface ITest {
    public void func();
}
```



```
public class A implements ITest {

    @java.lang.Override
    public void func() {

    }
}
```



```
INet net = new A();

```



default 关键字

```
public interface ITest {
	// 默认方法
	default void func1() {
		
	}
	// 静态方法
	static void func2() {
	
	}
}

// 调用接口中的默认方法
ITest.super.func1();
```

接口的继承，可以一对多





### 接口与抽象类的区别

1、 