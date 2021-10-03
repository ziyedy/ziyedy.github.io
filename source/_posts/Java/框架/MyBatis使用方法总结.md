---
title: MyBatis使用方法总结
date: 2020-12-22 15:59:21
tags:
	- ORM
categories:
	- Java
	- 框架
fileName: mybatis-basic-summary
---

## mybatis框架概述

MyBatis 本是 apache 的一个开源项目，主要用于减轻使用 JDBC 的复杂性（如JDBC中每次使用都需要重复创建一些对象，同时需要手动关闭连接以及手动实现ORM），同时可以将Java代码与sql语句分离，减小代码的耦合度。

MyBatis的使用可以参考其官方文档： https://mybatis.org/mybatis-3/zh/index.html

### mybatis完成的主要工作

* 注册数据库的驱动，即`Class.forName("com.mysql.jdbc.Driver");`
* 创建 JDBC 中必须的 `Connection` 、`Statement`、 `ResultSet` 等对象
* 从xml文件中获取相应sql，并执行sql语句，将 ResultSet 结果转化为 Java 对象
* 关闭资源

## 基本使用步骤

### 0、实验数据库

实验数据表 student 的基本字段如下：

![](http://cdn.ziyedy.top/MyBatis%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93/%E5%BB%BA%E6%B5%8B%E8%AF%95%E8%A1%A8.png)

### 1、创建实体类

类中属性名与列名需要保持一致

```java
public class Student {
    private Integer id;
    private String name;
    private Integer age;
    private String email;
}
```

### 2、创建dao接口

定义操作数据库的方法，用于对应mapper映射文件中的sql语句

```java
public interface StudentDao {
    Student selectStudentById(Integer id);	// 定义需要处理数据库的方法
    int insertStudent(Student student);
}
```

### 3、创建mapper映射文件

在dao接口的同目录下创建xml文件（mapper映射文件），名称与对应dao接口一致，**用于编写对应dao接口中方法的sql语句**

* 其中根目录 `<mapper>` 中的属性 `namespace` 通常设置为对应 dao 接口的全限定名称

* `<mapper>` 下可以使用 `<insert>` `<update>` `<delete>` `<select>`等标签，分别对应于sql语句中的相应子句，其中以 select 为例，其 id 属性用于唯一标识该标签内容，resultType 属性用于标识返回值类型，通常是实体类的全限定名
* 传入的参数可使用 `#{}` 占位符的方式用在标签内的 sql 语句中

*注意：`<select>` 等标签中的 `id` 值需要与 dao 接口中定义的方法名一致，这样mybatis就可以使用反射等机制自动创建 dao 接口的相应实现类。*

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.example.dao.StudentDao">

    <!-- 编写 sql 语句 -->
    <select id="selectStudentById" resultType="org.example.Student">
        select id, name, age, email from student where id=#{id}
    </select>
    
    <insert id="insertStudent">
        insert into student value(#{id}, #{name}, #{age}, #{email})
    </insert>

</mapper>
```

### 4、创建mybatis主配置文件

创建mybatis的主配置文件，放置在resources目录下。

主要用于配置mybatis相关的内容，比如日志、连接数据库所必要的一些信息

*此处配置了 logImpl 日志用于实验结果的展示*

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 使用日志，方便掌握运行结果 -->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <!-- 创建Connection对象，用于连接数据库 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8"/>
                <property name="username" value="root"/>
                <property name="password" value="shagds139"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="org/example/dao/StudentDao.xml"/>
    </mappers>
</configuration>
```

### 5、基本使用

* `Resources` 类：用于读取资源文件（加载解析mybatis主配置文件），返回的是IO流
* `SqlSessionFactoryBuilder`类：调用其 `build()` 方法可完成 `SqlSessionFactory` 的创建
* `SqlSessionFactory`接口：是一个重量级对象，且为线程安全的，因此一个应用只需要一个该对象，可使用`openSession()`方法创建 `SqlSession` 对象，默认该方法传入的参数为 false（手动提交），传入参数为 true 时，即`openSession(true)`，为自动提交事务
* `SqlSession` 接口：用于执行持久化操作，一个 `SqlSession` 对应一次数据库会话，其提供了大量的执行 sql 语句的方法
  * selectOne：执行select语句，最多得到一行记录
  * selectList：执行select语句，返回多行数据
  * insert：执行insert语句
  * update：执行update语句
  * delete：执行delete语句
  * commit：提交事务
  * rollback：回滚事务

基本使用流程如下：

```java
@Test
public void testInsert() throws IOException {
    String config = "mybatis.xml";
    InputStream inputStream = Resources.getResourceAsStream(config);	// 读取配置文件
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession session = factory.openSession();
    String sqlId = "org.example.dao.StudentDao.insertStudent";	// mapper文件中对应的sql语句

    Student newStudent = new Student(1002, "wuli", 10, "wuli@qq.com");
    int row = session.insert(sqlId, newStudent);	// 执行对应sql语句

    // mybatis默认执行sql语句是手动提交事务模式
    // insert后需要手动提交事务
    session.commit();

    session.close();
}
```

执行结果：

![](http://cdn.ziyedy.top/MyBatis%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93/update%E6%89%A7%E8%A1%8C%E7%BB%93%E6%9E%9C.png)

### 6、使用Dao代理

根据上述步骤，由于 dao 接口中的方法名与 mapper映射文件中的 对应的 id 是一致的，因此mybatis可以通过相关的信息自动帮助生成dao接口的实现类，获取该实现类的方法为 `session.getMapper(StudentDao.class);`，之后只需要执行对应的方法即可

```java
@Test
public void testProxy() throws IOException {
    String config = "mybatis.xml";
    InputStream inputStream = Resources.getResourceAsStream(config);
    SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession session = factory.openSession();

    StudentDao dao = session.getMapper(StudentDao.class);	// 获取实现类
    dao.selectByIdOrName(1, "dddy");	// 执行 dao 接口中对应的方法
    
    session.close();
}
```



## 传入参数使用

参数主要是指dao接口方法的形参，用于**通过Java程序将数据传入到mapper文件中的sql语句**中

### 传入单个简单类型参数

简单类型指：Java基本类型与String对象

此时使用占位符 `#{任意字符}` 即可使用，与**对应方法形参名无关**。如上一节中相关的示例

### 传入多个简单类型参数

#### 1、使用@Param注解

@Param：该注解为mybatis提供，放置在形参的前面，其value为自定义参数的名称，该名称在mapper文件中可作为该形参的对应标识。如下中`#{id}`即代表形参`Integer id`。

dao接口：

```java
List<Student> selectByIdOrName(@Param("id") Integer id,
                               @Param("name") String name);
```

mapper文件：

```xml
<select id="selectByIdOrName" resultType="org.example.Student">
    select id, name, age, email from student where id=#{id} or name =#{name}
</select>
```

#### 2、根据形参位置

参数位置： dao接口中方法的形参列表，从左往右，参数位置是 0，1，2 依此类推

语法格式：`#{arg0}`表示位置0的参数，`#{arg1}`表示位置1的参数

dao接口的方法：

```java
List<Student> selectByIdOrName(Integer id, String name);
```

mapper文件：

```xml
<select id="selectByIdOrName" resultType="org.example.Student">
    select id, name, age, email from student where id=#{arg0} or name =#{arg1}
</select>
```

### 传入对象

即dao方法形参为一个Java对象，该Java对象可以表示多个参数，通过`#{对象成员变量名}`可以直接使用

定义用于传参的Java对象：

```java
public class QueryParam {
    private Object p1;
    private Object p2;
}
```

dao接口的方法定义：

```java
List<Student> selectByQueryParam(QueryParam param);
```

mapper文件（通过直接使用 `#{p1}` 来获取 `QueryParam`对象中的 p1 属性）：

```xml
<!-- 简写版，mybatis 自动进行 ORM -->
<select id="selectByQueryParam" resultType="org.example.Student">
     select * from student where name=#{p1} or age=#{p2}
</select>

<!-- 完整版，指定类型映射 -->
<select id="selectByQueryParam" resultType="org.example.Student">
    select * from student where
    name=#{name, javaType=java.lang.String, jdbcType=VARCHAR}
    or
    age=#{age, javaType=java.lang.Integer, jdbcType=INTEGER}
</select>
```

### 传入Map

使用Map作为 dao 接口对应方法的参数，并且在 mapper 文件中使用 `#{key}`，通过 key 获取对应的 value 值。

dao接口方法：

```java
List<Student> selectStudentByMap(Map<String, Object> map);
```

mapper文件：

```xml
<select id="selectStudentByMap" resultType="org.example.Student">
     select * from student where name=#{name} or age=#{age}
</select>
```

核心方法调用：

```java
Map<String,Object> data = new HashMap<>();
data.put("name", "李思思");
data.put("age", 20);

List<Student> students = dao.selectStudentByMap(data);	// 传入map调用相应方法
```

### # 与 $ 占位符的区别

* #为占位符，表示该处用实际的参数值替代。即使用 `PrepareStatement` 对象执行 sql 语句，使用 `#{}` 代替 sql 语句中的 ?
* $为字符串替换，即表示该处使用${}中包含的“字符串”替换其所在位置，即使用 `Statement` 将sql语句和`${}`中的字符串连接**起来



## mapper文件常用标签（动态SQL）

> mybatis框架提供的标签，可以实现**同一个dao的方法， 根据不同的条件可以表示不同的sql语句**

### if 标签

当`<if>`标签中的值为true时，会将其包含的sql语句**拼接**到其所在的sql语句中。因此需要进行一些特殊处理避免拼接导致的sql语句出错（比如where后无条件或者多了or或and等关键字等情况）

```xml
<!-- 语法 -->
<if test="boolean判断结果">
    sql语句
</if>

<!-- 示例 -->
<select id="selectIf" resultType="org.example.Student">
    select * from student
    where 1=1
    <if test="name!=null and name!=''">
       and name=#{name}
    </if>
</select>
```

### where 标签

`if`标签需要在 where 子句后手动添加一个 `1==1` 的永为真的子句（否则若 if 条件全为 false 等情况会出现sql语句中多出where或or等字段的情况造成sql语句执行出错）。因此可以使用 `<where>` 标签，**当满足查询条件时会自动添加上 where 子句**

```xml
<!-- 语法 -->
<where>
    <if test="条件1">sql语句1</if>
    其他动态sql语句
</where>

<!-- 示例 -->
<select id="selectWhere" resultType="org.example.Student">
    select * from student
    <where>
        <if test="name!=null and name!=''">
            or name = #{name}
        </if>

        <if test="age >0">
            or age &lt; #{age}
        </if>
    </where>

</select>
```

### foreach 标签

该标签主要用于实现对集合或数组之间的遍历，通常用在sql的 `in` 子句处，用于实现相应的sql字符串的拼接

如下为手动实现 sql 语句的拼接：

```java
List<Integer> idList = new ArrayList<>();
idList.add(1001);
idList.add(1002);
idList.add(1003);

StringBuffer sql= new StringBuffer("");
sql.append("select * from student where id in ");

sql.append("(");
for(int i=0; i<idList.size(); i++){
    Integer item  = idList.get(i);	// item是集合成员
    sql.append(item);	//添加成员到 sql字符串
    sql.append(","); 	//集合成员之间的分隔符
}
sql.deleteCharAt( sql.length()-1) ;

//循环之后，加入 )
sql.append(")");
```

使用 `<foreach>` 标签的基本语法：

```xml
<!-- 语法 -->
< foreach collection="集合类型，list表示集合，array表示数组" 
         open="循环开始时的字符" close="循环结束时字符" 
         item="用于标识集合中的成员" separator="集合成员之间的分隔符">
    #{item属性定义的值}
</ foreach>
```

#### 输入参数为普通类型

dao 方法定义，输入的参数 为 `Integer`

```java
List<Student> selectForeach1(List<Integer> idList);
```

mapper文件：

```xml
<select id="selectForeach1" resultType="org.example.Student">
    select * from student where id in
    <foreach collection="list" open="(" close=")" separator="," item="id" >
        #{id}
    </foreach>
</select>
```

执行方法：

```java
List<Integer> idList = new ArrayList<>();
idList.add(2);
idList.add(3);
idList.add(4);
dao.selectForeach1(idList);
```

执行结果：

![](http://cdn.ziyedy.top/MyBatis%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93/foreach1.png)

#### 输入参数为引用类型

dao方法定义，输入的基本参数为 `Student`  类型

```java
List<Student> selectForeach2(List<Student> studentList);
```

mapper文件：采用了 `#{item.id}`的写法，表示 `Student`  中的 `id` 属性

```xml
<select id="selectForeach2" resultType="org.example.Student">
    select * from student
    <if test="list != null and list.size > 0">
        where id in 
        <foreach collection="list" open="(" close=")" separator="," item="item">
            #{item.id}
        </foreach>
    </if>
</select>
```

执行方法：

```java
List<Student> stuList = new ArrayList<>();
stuList.add(new Student(1, "ziye", 10, "123"));
stuList.add(new Student(2, "123", 11, "123"));
dao.selectForeach2(stuList);
```

执行结果：

![](http://cdn.ziyedy.top/MyBatis%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93/foreach2.png)

### sql标签

`<sql>` 标签**标识一段sql语句**，可以在其他位置通过该标签的 `id` 属性值结合 `include` 标签实现对其标识的sql语句的复用

```xml
<!-- 定义代码片段 -->
<sql id="selectStudent">
    select * from student
</sql>

<!-- 使用代码片段 -->
<select id="selectIf" resultType="org.example.Student">
    <!-- select * from student -->
    <include refid="selectStudent" />
    
    where 1=1
    <if test="name!=null and name!=''">
       and name=#{name}
    </if>
</select>
```



