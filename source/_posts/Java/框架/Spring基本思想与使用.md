---
title: Spring基本思想与使用
date: 2021-04-23 14:39:22
tags:
	- Spring
categories:
	- Java
	- 框架
fileName: Spring-basic-use
---

## Spring基本思想

* 控制反转（IoC）：实现了代码的“解耦”，让对象与对象、模块与模块之间不使用代码关联，而是通过配置进行说明（不再需要在代码中手动的维护对象与模块之间的关系了）
* 面向切面编程（AOP）：使系统级服务得到了最大复用，实现了其与业务代码的解耦合，也降低了代码的冗余度

### 控制反转IoC

> 即将传统上由程序代码直接操控的对象的创建、属性赋值、生命周期等对象控制权交给容器
>
> 通俗而言就是通过容器实现对象的创建、属性赋值、依赖管理等等

IoC是一种思想，其有多种实现方式，**Spring采用的实现方式是依赖注入（DI，Dependency Injection）**的方式

#### 依赖注入DI

* 依赖：classA 类中含有 classB 的实例，在 classA 中调用 classB 的方法完成功能，即 classA 对 classB 有依赖

* 依赖注入：指程序运行过程中，若需要调用另一个对象协助时，无须在代码中创建被调用者，而是依赖于外部容器，由外部容器创建后传递给程序（Spring的依赖注入对调用者与被调用者几乎没有任何要求，完全支持对象之间的依赖关系的管理，其底层处理了可能出现的循环依赖等问题）

### 面向切面编程AOP

> AOP（Aspect Orient Programming），即通过运行期间动态代理实现程序功能的一种技术
>
> 通俗而言就是将交叉业务逻辑（即通用的，与主业务无关的代码，如日志输出、安全检查等等）封装成切面，利用AOP技术将切面织入到主业务逻辑中

通过使用AOP的思想，能够大大降低各业务之间的耦合度，提高程序的可重用性

#### 相关术语

* 切面（Aspect）：泛指交叉业务逻辑
* 连接点（JoinPoint）：指可以被切面织入的具体方法（需要使用切面的方法都是连接点）
* 切入点（Pointcut）：一个或多个连接点的集合（可以通过通配符等表明一组连接点）
* 通知（Advice）：表示切面的执行时间（比如之前、之后等等）

### Spring的优势

* 轻量级，相关的jar包都比较小
* 针对接口编程，解耦合（IoC）
* AOP编程的支持
* 方便集成各种优秀框架（Spring提供了对各类优秀框架如MyBatis的支持），简化了框架的使用



## 实现IoC

### 基于XML配置文件

#### 1、配置文件声明对象（Bean）

配置文件的根标签是 `<beans>` ，主要包括一些 `.xsd` 的约束文件说明。

`<beans>` 中包含 `<bean>` 声明，**Bean**即为交由Spring容器管理的Java对象，其声明方式如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--声明对象
        id：标识对象的唯一值(Spring可以提供默认名称)
        class：类的全限定名称，用于通过反射机制创建对象，不能是接口
    -->
    <bean id="ServiceImpl" class="org.example.impl.ServiceImpl" />
</beans>
```

*注：Spring默认调用类的无参构造方法创建对象，同时读取完配置文件后，会一次性创建好所有的Bean对象，放入容器维护的相应map中*

#### 2.1、利用set方法实现DI

> Spring调用类的**无参构造方法**创建对象实例后，调用类中的 `set` 方法，在set方法中可以完成属性赋值（set方法内部逻辑可以自己实现，Spring只负责调用相应set方法）

*当类中没有set方法时，直接使用set注入会报错*

```xml
<bean id="ServiceImpl" class="org.example.impl.ServiceImpl" >
    <!-- 简单类型赋值 -->
    <property name="str" value="ziye" />
    <property name="num" value="10" />
	<!-- 引用类型赋值, ref为对象对应的 bean 的 id -->
    <property name="util" ref="Util" />
</bean>

<bean id="Util" class="org.example.impl.Util" >
    <property name="name" value="yezi" />
    <property name="times" value="123" />
</bean>
```

#### 2.2、利用构造方法实现DI

> spring调用类中的*有参数构造方法*， 在创建对象的同时，给属性赋值

可以使用 `name` 指定构造函数中形参的名称，也可以使用 `index` 来指定形参在方法中的位置，不使用的话则默认使用形参的排列顺序

```xml
<bean id="ServiceImpl" class="org.example.impl.ServiceImpl" >
    <!-- 简单类型赋值 -->
    <constructor-arg name="str" value="ziye" />
    <constructor-arg name="num" value="10" />
    <!-- 引用类型赋值, ref为对象对应的 bean 的 id -->
    <constructor-arg name="util" ref="Util" />
</bean>
```

#### 2.3、引用类型的自动注入

> 自动注入：Spring可以根据某些规则（byName，byType）给**引用类型**完成赋值。 

* byName（按名称注入）： Java类中引用类型属性名称和Spring容器中bean的id名称一样的，且数据类型也是一样的。设置 `autowire="byName"`

  ```xml
  <bean id="ServiceImpl" class="org.example.impl.ServiceImpl" autowire="byName" />
  ```

* byType（按类型注入）：Java类中引用类型的数据类型和Spring容器中bean的class值是同源关系的，这样的bean赋值给引用类型。

  *注： 在xml配置文件中， 符合条件的对象，只能有一个。 多余一个是报错的*

  ```xml
  <bean id="ServiceImpl" class="org.example.impl.ServiceImpl" autowire="byType" />
  ```

### 基于注解

使用注解完成DI，除去在相应位置加上对应注解外，必须在Spring的配置文件中加入**组件扫描器**，如下：

```xml
<!-- 使用注解必须加上组件扫描器 -->
<context:component-scan base-package="org.example" />
```

#### 定义Bean的注解（@Component）

在类名之上加上`@Component`注解，相当于在配置文件中使用`<bean>`对该类进行了声明，同时该注解中的value表示对象名称，相当于`<bean>`中的`id`值（如果不写的话默认id为类名的首字母小写）

此外，Spring还提供了3个创建对象的注解，这些注解还有其特定的含义

* `@Repository`：通常放在dao接口的实现类上，表示创建持久层对象，能访问数据库
* `@Service`：通常放在业务层接口的实现类上，表示创建业务层对象，可以加入事务的功能
* `@Controller`：放在控制器类的上面，表示创建控制层对象

#### 简单类型属性注入（@Value）

使用`@Value`注解完成，该注解可放置在属性定义的上面，也可以放置在set方法的上面，如下：

```java
@Value("ziye")
private String str;

@Value("10")
private int num;
```

同时使用该注解可以**从外部配置文件中得到相应的值进行注入**

1. 设置配置文件（`conf.properties`）示例如下：

   ```
   str=ziye
   num=12
   ```

2. 读取外部配置文件（根据其路径，如下为）

   ```xml
   <!-- 读取外部属性配置文件（properties文件） -->
   <context:property-placeholder location="classpath:/conf.properties" />
   ```

3. 使用对应语法进行属性注入（`"${}"`）

   ```java
   @Value("${str}")
   private String str;
   
   @Value("${num}")
   private int num;
   ```

#### 引用类型注入（@Autowired）

在引用属性上使用`@Autowired`，该注解默认会使用**按类型的自动注入（byType）**的方法

```java
@Autowired
private Util util;
```

也可以联合使用`@Autowired`与`@Qualifier`两个注解，实现**按名称的自动注入（byName），即对应bean的id**

```java
@Autowired
@Qualifier("util")
private Util util;
```

也可以使用JDK自带的`@Resource`注解实现注入，*该注解默认按名称注入，若按名称无法注入，则按照类型进行注入*。按照名称进行注入需要指定其`name`属性（即Bean的id），示例：`@Resource(name="util")`

### Bean的使用

Spring根据id，class创建对象， 把对象放入Spring的一个map对象，具体操作主要有以下四步：

```java
// 1、配置文件的路径
String config = "beans.xml";
// 2、读取配置文件,创建容器对象，ApplicationContext 表示 Spring 容器对象
ApplicationContext ctx = new ClassPathXmlApplicationContext(config);
// 3、获取id名为ServiceImpl的对象
Service service = (Service) ctx.getBean("ServiceImpl");
// 4、执行对象的方法
service.method();
```

其中`ApplicationContext`接口（容器）默认为无参构造，同时创建容器对象时，**默认会把配置文件中所有对象都创建出来**（之后需要使用这些对象，只需要从内存中直接获取即可，执行效率高但占用内存）

利用`ApplicationContext`对象可以获取容器中对象的一些基本信息，常用方法如下：

```java
// 获取容器中定义对象的数量
int nums = ctx.getBeanDefinitionCount();

// 获取容器中定义的对象名称
String names[] = ctx.getBeanDefinitionNames();
```



## 实现AOP

在Spring中使用AOP开发时，一般使用**AspectJ**的实现方式。其中AspectJ是一个优秀的面向切面的框架（Eclipse的开源项目）

### 切入点表达式

#### 切入点表达式

AspectJ 定义了专门的表达式用于指定切入点，定义如下：

```
execution(访问权限 方法返回值 方法声明(参数) 异常类型)
```

其中方法返回值、方法声明（参数）是不可省略的，方法声明可以用全限定名也可以不使用

除此之外，切入点表达式也可以使用一些通配符：

| 符号 | 意义                                                         |
| ---- | ------------------------------------------------------------ |
| *    | 0至多个任意字符                                              |
| ..   | 用在方法参数中，表示任意多个参数<br />用在包名后，表示当前包及其子包 |
| +    | 用在类名后，表示当前类及其子类<br />用在接口后，表示当前接口及其实现类 |

#### 共享切入点表达式

如果多个切面通知方法所使用的切入点是一样的话，实现共享切入点表达式能够有效的解耦合。

可以使用`@Pointcut`注解实现定义和管理切入点。该注解可以放置在在一个自定义方法的上面，**该方法名可以看做是切入点表达式的别名**。具体使用如下：

```java
@After(value = "pt()")	// 共享pt对应的切入点表达式
public void myAfter(){
    
}

// 自定义对应切入点的表达式，pt
@Pointcut("execution(* *..SomeServiceImpl.doThird(..))")
private void pt(){
    // 无需代码
}
```

### 通知类型

#### 前置通知@Before

前置通知方法在目标方法之前执行，并且不会影响目标方法的执行，也不会修改目标方法的执行结果，使用`@Before`实现

`@Before`注解放置在相应前置通知方法的上面，其中value为切入点表达式，用于需要执行相应切面方法的方法

前置方法主要有以下方面需要注意：

1. 方法是public，且返回值为void

2. 方法可以有参数，若有的话第一个参数必须为`JoinPoint`（表示正在执行的业务方法，相当于反射中的 `Method`，可用于获取方法执行时的信息，如方法名称、参数集合等等）

#### 后置通知@AfterReturning

后置通知方法在目标方法之后执行，**能获取目标方法的执行结果，但不会影响目标方法的执行**，使用`@AfterReturning`实现

`@AfterReturning`放置在方法上面，其中value为切入点表达式，returning为自定义的变量，表示目标方法的返回值（**自定义变量名称必须和通知方法的形参名一样**）

1. 方法限定名public，返回值void
2. 方法有参数（通常使用Object类型作为参数，用于表示目标方法的返回值）

#### 环绕通知@Around

环绕通知在目标方法的前后都能够插入功能，并且**能够控制目标方法是否执行，也能够修改目标方法的执行结果**，使用`@Around`实现

`@Around`放置在方法之上，其中value为切入点表达式

1. 方法限定名public，且必须有返回值（通常定义为Object对象）
2. 方法必须有`ProceedingJoinPoint`参数（继承自`JoinPoint`，可用于执行目标方法，相当于`Method.invoke()`），使用 `proceedingJoinPoint.proceed();` 可执行方法

#### 异常通知@AfterThrowing

异常通知用`@AfterThrowing`注解实现，异常通知方法使用public限定名，无返回值，具有`Exception`类型的参数。具有以下特征：

1. 在目标方法抛出异常后执行的，没有异常不执行
2. 能获取到目标方法的异常信息
3. 不是异常处理程序。可以得到发生异常的通知（常用作异常的监控程序）

其处理流程相当于：

```java
try {
	// 业务方法
} catch(Exceptoin e) {
	myAfterThrowing(e);	// 切面方法
}
```

`@AfterThrowing`注解中value为切入点表达式，throwing表示目标方法抛出的异常，其值必须与通知方法形参名一致

#### 最终通知@After

最终通知方法在目标方法之后执行，且无论是否抛出异常都会继续执行，可以用来进行程序的收尾工作（如清除临时数据等）。该通知使用`@After`注解完成，其中value为切入点表达式。

其处理流程相当于：

```java
try {
	// 业务方法
} finally {
	myAfter();	// 切面方法
}
```

### AOP使用demo

1、加入相关依赖，主要是`spring-aspects`依赖

2、**使用`@Aspect`注解，用于标识当前类是一个实现切面功能的类**，该注解放置在类的上面

3、在配置文件中声明自动代理器，用于创建目标对象的代理，同时定义对应的业务类与切面类的Bean

```xml
<!-- 声明自动代理生成器 -->
<aop:aspectj-autoproxy />
```

4、根据需要编写相关代码

切面类：

```java
package org.example.aop.handle;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;

@Aspect // 表示该类为AOP类
public class MyAspect {
    // 前置通知示例
    @Before("execution(public void org.example.aop.Service.doSome(String, int))")
    public void before1() {
        System.out.println("--------- 前置通知1");
    }

    // JoinPoint: 表示正在执行的业务方法， 相当于反射中的 Method
    @Before("execution(public * org.example.aop.Service.doSome(..))")
    public void before2(JoinPoint joinPoint) {
        System.out.println("--------- 前置通知2，获取目标方法定义：" + joinPoint.getSignature());

    }

    // 后置通知示例 其中returning中的res必须 与 形参中的res 一致
    @AfterReturning(value = "execution(public int org.example.aop.Service.doSome2(..))",
            returning = "res")
    public void afterReturning(Object res) {
        System.out.println("--------- 后置通知，拿到方法执行结果：" + res);
    }

    // 环绕通知示例
    @Around("execution(public * doSome3(..))")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("--------- 执行环绕通知around");
        int res = (int) proceedingJoinPoint.proceed();  // 执行方法 并 得到执行结果
        return 1000;    // 环绕通知修改方法调用结果
    }

    // 异常通知示例，其中throwing中的ex必须与形参名ex一致
    @AfterThrowing(value = "execution(public * doSome4(..))", throwing = "ex")
    public void afterThrowing(Exception ex) {
        System.out.println("异常通知，异常原因：" + ex.getMessage());
    }

}
```

业务类：

```java
package org.example.aop;

public class Service {
    public void doSome(String str, int num) {
        System.out.println("执行doSome1方法： " + str + ": " + num);
    }

    public int doSome2(int num) {
        System.out.println("执行doSome2方法，执行结果应为： " + num * 2);
        return num * 2;
    }

    public int doSome3(int num) {
        System.out.println("执行doSome3方法，返回值应为：" + num);
        return num;
    }

    public void deSome4() {
        System.out.println("执行方法测试异常：" + (1 / 0));
    }
}
```

主函数：

```java
package org.example;

import org.example.aop.Service;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        String config = "beans.xml";
        ApplicationContext ctx = new ClassPathXmlApplicationContext(config);    // 读取配置文件
        Service service = (Service) ctx.getBean("aopService");

        // 前置通知测试
        service.doSome("ziye", 22);
        System.out.println();

        // 后置通知测试
        service.doSome2(10);
        System.out.println();

        // 环绕通知测试
        int res = service.doSome3(10);
        System.out.println("实际值为：" + res);
        System.out.println();

        // 查看生成的代理类型
        System.out.println(service.getClass().getName());
    }
}
```

运行结果：





## 实现事务