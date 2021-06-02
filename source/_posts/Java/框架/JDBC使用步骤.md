---
title: JDBC使用步骤
date: 2021-01-30 23:33:07
tags:
  - 数据库
categories:
  - Java
  - 框架
fileName: JDBC-summary
---

## 0、数据库连接配置

新建一个 `jdbc.properties` 文件进行配置，主要信息有数据库的驱动，以及连接数据库的必要的信息。

```
driver = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8
username = root
password = shagds139
```

进行驱动的加载

```java
ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
String driver = bundle.getString("driver");
String url = bundle.getString("url");
String username = bundle.getString("username");
String password = bundle.getString("password");
```

## 1、注册驱动

说明要连接什么数据库，即使用各个数据库厂商实现的 Java 定义的用于连接数据库的接口。主要有两种实现方法

```java
DriverManager.registerDriver(new com.mysql.jdbc.Driver());
```

通常使用以下方法进行驱动的加载。主要好处有通过反射可以使用配置文件的方式传入数据库驱动，同时`Class.forName()`的执行会导致类的加载，因此可以利用类加载的动作实现其静态代码块的执行

```java
Class.forName(driver);
```

## 2、获取连接

表示 JVM 的进程和数据库进程之间的通道打开了，这属于进程之间的重量级通信，因此不能频繁创建。`Connection` 在JDBC中用于表示数据库的连接

```java
Connection connection = null;
connection = DriverManager.getConnection(url, username, password);
```

`Connection` 有如下一些常用方法：

| 方法                              | 描述                                           |
| --------------------------------- | ---------------------------------------------- |
| createStatement()                 | 创建向数据库发送sql的statement对象             |
| prepareStatement(sql)             | 创建向数据库发送预编译sql的PrepareSatement对象 |
| prepareCall(sql)                  | 创建执行存储过程的callableStatement对象        |
| setAutoCommit(boolean autoCommit) | 设置事务是否自动提交                           |
| commit()                          | 提交事务                                       |
| rollback()                        | 回滚事务                                       |

其中 **JDBC 事务默认是自动提交**的，即执行任意一条DML语句，就自动提交一次，要实现手动提交事务通常使用如下步骤实现

```java
try {
    connection.setAutoCommit(false);	// 关闭自动提交
    
    // 执行 sql 语句
    
    connection.commit();	// 手动提交
    
} catch (Exception e) {
    try {
        connection.rollback();	// 出现异常则进行回滚
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    }
    e.printStackTrace();
}
```



## 3、获取数据库操作对象

获取数据库操作对象，即用于执行 sql 语句的对象

### sql 语句拼接问题（注入攻击）

如使用如下语句进行用户名与密码的验证，**输入包含恒为真的条件**可能造成注入攻击，如下

```java
String sql = "select * from user where username = '" + name + "' and password = '" + pwd + "'";
```

输入 `ziye' or '1'='1` 即可以将 sql 语句拼接为如下语句，恒为真

```sql
select * from user where username = 'ziye' and password = 'ziye' or '1'='1'
```

### Statement

```java
Statement statement = null;
statement = connection.createStatement();
```

`Statement` 有如下常用方法：

| 方法                      | 含义                                                         |
| ------------------------- | ------------------------------------------------------------ |
| executeQuery(String sql)  | 用于向数据发送查询语句，返回 `ResultSet`                     |
| executeUpdate(String sql) | 用于向数据库发送insert、update、delete等DML语句，返回记录条数 |
| execute(String sql)       | 用于向数据库发送任意sql语句                                  |
| addBatch(String sql)      | 把多条sql语句放到一个批处理中                                |
| executeBatch()            | 向数据库发送一批sql语句执行                                  |

### PreperedStatement

是 `Statement` 的子类，相对于 `Statement` ，其可通过占位符等操作避免 sql注入攻击等问题。同时 `statement` 编译一次执行一次，而 `PreparedStatement` 可进行预编译，编译一次执行n次，效率较高。同时由于需要向占位符传入数据，`PreparedStatement`在Java程序的编译阶段会进行检查

## 4、执行 sql 语句

### Statement 常规执行

```java
// 执行查询语句
String sql = "select * from student";
resultSet = statement.executeQuery(sql);

// 执行 DML 语句
String sql1 = "insert into student(id, name, age, email) value(111, 'test', 10, 'zzz@qq.com')";
String sql2 = "delete from student where id=111";
String sql3 = "update student set name = 'modify', email = 'null' where id = 4";
int count = statement.executeUpdate(sql1); 
```

### PreperedStatement 使用占位符 ?

传入的信息只作为数值传入，而不参与 sql 编译即可避免注入攻击，可以使用 `prepareStatement` 完成

```java
// 使用 ? 作为占位符
String sql = "select * from user where username = ? and password = ?";
ps = connection.prepareStatement(sql);

ps.setString(1, name);	// 给占位符传值，第一个问号的下标为 1
ps.setString(2, pwd);	// 第二个问号的下标为 2

resultSet = ps.executeQuery();	// 获取结果
```

## 5、处理查询结果集

使用 `select` 查询语句后会得到结果集，需要对其进行处理

`ResultSet` 封装了执行结果，只需要迭代的将结果取出即可，示例如下：

```java
List<Student> studentList = new ArrayList<>();
while (resultSet.next()) {
    // 对查询结果进行相应的处理
    Student stu = new Student(resultSet.getInt("id"),
                              resultSet.getString("name"),
                              resultSet.getInt("age"),
                              resultSet.getString("email"));
    studentList.add(stu);
}
```

## 6、释放资源

Jdbc程序运行完后，要释放程序在运行过程中创建的那些与数据库进行交互的对象，这些对象通常是`ResultSet`、`Statement`和 `Connection` 对象，要**按照创建的顺序**依次进行 关闭

*为确保资源释放代码能运行，资源释放代码也一定要放在finally语句*

```java
try {
    // 必须要依次关闭
    if (resultSet != null) {
        resultSet.close();
    }
    if (statement != null) {
        statement.close();
    }
    if (connection != null) {
        connection.close();
    }
} catch (Exception e) {
    e.printStackTrace();
}
```

## 完整示例

```java
public static void main(String[] args) {
    Connection connection = null;
    Statement statement = null;
    ResultSet resultSet = null;


    ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
    String driver = bundle.getString("driver");
    String url = bundle.getString("url");
    String username = bundle.getString("username");
    String password = bundle.getString("password");
    
    try {
        // 1、注册驱动
        Class.forName(driver);
        // DriverManager.registerDriver(new com.mysql.jdbc.Driver());

        // 2、获取数据库连接对象
        connection = DriverManager.getConnection(url, username, password);

        // 3、数据库操作对象
        statement = connection.createStatement();

        // 4、执行 sql 语句 （以执行DQL语句为例）
        String sql = "select * from student";
        resultSet = statement.executeQuery(sql);
        
        // 5、处理查询结果集
        List<Student> studentList = new ArrayList<>();
        while (resultSet.next()) {
            Student stu = new Student(resultSet.getInt("id"),
                                      resultSet.getString("name"),
                                      resultSet.getInt("age"),
                                      resultSet.getString("email"));
            studentList.add(stu);
        }

        for (Student stu : studentList) {
            System.out.println(stu);
        }
        
    } catch (Exception e) {
        e.printStackTrace();
        
    } finally {
        // 6、释放资源
        try {
            if (resultSet != null) {
                resultSet.close();
            }
            if (statement != null) {
                statement.close();
            }
            if (connection != null) {
                connection.close();
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
