---
title: Servlet基础总结
date: 2021-01-28 13:03:39
tags:
	- Java
	- Web
categories:
	- Java
	- Java Web
fileName: servlet-basic-summary
---

# Servlet基础

## 概念

> Servlet是 Server 与 Applet 的缩写，是用Java语言编写的*运行在服务器端*，由服务器调用执行，按照 Servlet 标准来开发的类，用来完成B/S架构下，客户端请求的响应处理。
>
> Servlet 本质上也是 Java 类，但要遵循 Servlet 规范进行编写，它的创建、使用、销毁都由 Servlet 容器进行管理，所谓 Servlet 容器即为提供了 Servlet 功能的服务器，常见的有Tomcat、Jetty、JBoss等等（即服务器会自动调用Servlet程序）
>
> 同时Servlet与HTTP紧密相连，可以处理HTTP协议相关的所有内容，因此在Java Web开发中应用广泛

## Servlet实现

### 继承 `HttpServlet` 类

> 该类中已经完成了通信的规则，我们只需要进行业务的实现即可

在整个Servlet程序之中最重要的就是Servlet接口，在此接口下定义了一个GenericServlet的子类，但是一般不会直接继承此类，而是根据所使用的协议选择GenericServlet的子类继承，例如：现在是采用HTTP协议处理的，所以一般而言当需要使用HTTP协议操作时用户自定义的Servlet类都要继承HttpServlet类。

### 重写`service`方法

该方法用来处理请求的操作，也可以重写`doGet`、`doPost`等方法，`service`方法的本质其实是调用了这两个方法。

### 设置注解

> 可以通过注解指定访问路径（在Servlet 3.0之后版本支持，否则得配置`web.xml`文件）

示例如下，表示该网页位于`root/gg/ss`下，（name只是作为一个标识，没有太大实际作用）

```
@WebServlet("/gg/ss")
@WebServlet(value="/gg/ss")
@WebServlet(urlPatterns="/gg/ss")
@WebServlet(name="Servlet01",value="/gg/ss")
```

也可以配置多路径访问，如下表示两个路径都能够进行访问。

```
@WebServlet(name="Servlet01",value={"/ss",'/gg'})
```

### IDEA中更改Web项目根目录

![](http://cdn.ziyedy.top/Servlet%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/IDEA%E8%AE%BE%E7%BD%AEURL%E5%9C%B0%E5%9D%80.png)

## Servlet生命周期

> Servlet没有main()方法，不能独立运行，其运行完全由Servlet容器来控制和调度。
>
> 其生命周期则是指Servlet容器何时创建servlet实例、何时调用其方法进行请求的处理、何时销毁其实例的整个过程。
>
> Servlet类加载——>实例化——>服务——> 销毁

* 实例化与初始化

  请求到达容器时，容器查找该servlet对象是否存在，若不存在，则创建实例并初始化。默认方法`init()`

* 就绪/调用/服务阶段

  调用`service()`方法处理请求，在生命周期中可以被多次调用，`HttpServlet`中的该方法根据请求方式调用doGet和doPost方法

* 销毁时机

  当容器关闭时，将Servlet实例进行销毁，`destroy()`方法。



# 请求与响应

## HttpServletRequest对象

> HttpServletRequest对象，主要作用是用来接收客户端发送过来的请求信息（如请求参数、表单等信息）
>
> `service()`方法中形参接收的是 HttpServletRequest 接口的实例化对象，表示该对象主要应用在 HTTP 协议上，该对象是由 Tomcat 封装好传递过来，我们只需要调用相应方法取出信息进行处理即可
>
> HttpServletRequest 是 `ServletRequest` 唯一的子接口

### 常用方法

获取基本信息的方法。

| 方法名           | 功能                                                 |
| ---------------- | ---------------------------------------------------- |
| getRequestURL()  | 获取客户端请求的完整URL（从http开始，到?前面结束）   |
| getRequestURI()  | 获取客户端请求的部分URL（从站点名开始，到?前面结束） |
| getQueryString() | 获取请求行中的参数部分（?后面的部分，没有返回null）  |
| getMethod()      | 获取客户端请求方法（GET、POST）                      |
| getProtocol()    | 获取HTTP版本号                                       |
| getContextPath() | 获取webapp名字                                       |

### 处理表单输入

> 使用`getParameter()`方法获取指定名称的对应值
>
> 使用`getParameterValues()`方法获取指定名称所有对应值，返回数组

HTML表单如下：

```
<form action="test" method="get">
    请输入：
    <input type="text" name="info" />
    <input type="submit" value="提交" />
</form>
```

![](http://cdn.ziyedy.top/Servlet%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/%E5%A4%84%E7%90%86%E8%A1%A8%E5%8D%95%E8%BE%93%E5%85%A5.png)

Servlet处理逻辑：

```
@WebServlet("/test")    // 子目录
public class test extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse resp)throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8"); // 解决中文乱码
        String info = req.getParameter("info");	// 获取名为info的值
        PrintWriter out=resp.getWriter();
        out.println("<html><head><title>test</title></head>");
        out.println("<body><h1>"+info+"</h1>");
        out.println("</body></html");
        out.close();
    }
}
```

![](http://cdn.ziyedy.top/Servlet%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/%E5%A4%84%E7%90%86%E8%A1%A8%E5%8D%95%E7%BB%93%E6%9E%9C.png)

## HttpServletResponse对象

> 主要用于向客户端输出数据
>
> service()方法中形参接收的是 HttpServletResponse 接口的实例化对象，这个对象中封装了向客户端发送数据、发送响应头、发送响应状态码的方法

### 响应数据

> 通过HttpServletResponse对象直接使用*输出流*进行响应，响应的数据回到客户端被浏览器解析
>
> `getWriter()` 获取字符流
>
> `getOutputStream()` 获取字节流，可以响应一切数据

示例如下：

```
// 字符输出流
PrintWriter writer = response.getWriter();
writer.write("<h2>Hello</h2>");
```

```
// 字节输出流
ServletOutputStream out = response.getOutputStream();
out.write("<h2>Hello</h2>".getBytes());
```

设置响应类型，默认是字符串：

```
// 设置响应MIME类型
response.setHeader("content-type","text/html"); // html
```

## 处理中文乱码

解决服务器返回页面中文乱码问题

```
response.setContentType("text/html;charset=UTF-8");
```

解决post方式请求表单参数中文乱码问题，在接收所有数据前设定

```
request.setCharacterEncoding("UTF-8");
```

Tomcat9.0已经解决了get方式请求中文乱码问题，可以不需要做此配置。

## Servlet跳转

### 请求转发

> 一种服务器行为，只存在一次请求（因此request数据共享），地址栏不会发生改变
>
> 同时只能跳转到该站点下的目录

```
request.getRequestDispatcher(url).forward(request,response);
```

### 重定向

> 服务端指导，客户端行为，存在两次请求，且地址栏会发生改变
>
> 可以跳转到任意目录，比如www.baidu.com
>
> 第一次请求会获得3开头的响应码，并且含有一个 location 头信息（代表重定向的地址）

```
// 重定向跳转到index.jsp
response.sendRedirect("index.jsp");
```

## ServletContext对象

> 在Web容器启动的时候，会为每一个 Web 应用程序创建一个对应的 `ServletContext` 对象，每个Web应用都有且只有一个`ServletContext`对象
>
> 主要作用：1、作为域对象在整个Web应用共享数据；2、保存了当前应用程序的相关信息

### 获取与常用方法

```
// 通过 request 对象获取
ServletContext servletContext = request.getServletContext();

// 通过 session 对象获取
ServletContext servletContext = request.getSession().getServletContext();

// 通过 servletConfig 对象获取
ServletConfig servletConfig = getServletConfig();
ServletContext servletContext = servletConfig.getServletContext();

// 直接通过Servlet类获取
ServletContext servletContext = getServletContext();

// 获取项目存放的真实路径
String realPath = request.getServletContext().getRealPath("/");
// 获取当前服务器的版本信息
String serverInfo = request.getServletContext().getServerInfo();
```

### ServletContext域对象使用

```
// 获取ServletContext对象
ServletContext servletContext = request.getServletContext();
// 设置域对象
servletContext.setAttribute("name","zhangsan");
// 获取域对象
String name = (String) servletContext.getAttribute("name");
// 移除域对象
servletContext.removeAttribute("name");
```



# 会话

## Cookie

> `javax.servlet.http.Cookie`

### 特点

* Cookie只保存在当前浏览器中
* Cookie中不能出现中文，需要进行编解码操作
* 如果服务器端发送重复的Cookie那么会覆盖原有的Cookie
* 浏览器存放Cookie的数量是有上限的

### Cookie的创建与发送

```
// 创建Cookie对象
Cookie cookie = new Cookie("id", 0);
// 发送Cookie对象
response.addCookie(cookie);
```

![](http://cdn.ziyedy.top/Servlet%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/%E5%88%9B%E5%BB%BAcookie.png)

如上，即创建了我们自己的cookie

```
@WebServlet("/cookie1")
public class Cookie1 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 创建Cookie对象
        Cookie cookie = new Cookie("id", "id");
        // 发送Cookie对象
        response.addCookie(cookie);
    }
}
```

### Cookie的获取

> Servlet提供了一个 `getCookies()`的方法用来获取客户端回传的所有 cookie 组成的一个*数组*，如果需要获取单个 cookie 则需要通过遍历，`getName()`获取 Cookie 的名称，`getValue()`获取 Cookie 的值



```
// 获取Cookie数组
Cookie[] cookies = request.getCookies();
// 判断数组是否为空
if (cookies != null && cookies.length > 0) {
  	// 遍历Cookie数组
  	for (Cookie cookie : cookies){
  		// 判断并获取
    	System.out.println(cookie.getName());
    	System.out.println(cookie.getValue());
	}
}
```

### Cookie的路径

> Cookie的`setPath()`方法设置cookie的路径，这个路径直接决定服务器的请求是否会从浏览器中加载某些cookie
>
> 默认情况下只有当前项目能获取当前项目产生的cookie

```
// 设置路径为"/"，表示在当前服务器下任何项目都可访问到该Cookie对象
cookie.setPath("/");

// 只有gg项目能访问该cookie
cookie.setPath("/gg");

// 只有/gg/ss目录下的文件才能访问到cookie
cookie.setPath("/gg/ss");
```

### Cookie的到期时间

> Cookie默认当前浏览器关闭即失效，也可以通过 `setMaxAge(int time)`方法设定 cookie 的最大有效时间，以秒为单位
>
> time为负数，表示不存储该Cookie，浏览器关闭即失效
>
> time为0，表示删除该cookie
>
> time为正数则为存储的秒数



## Session

> session 本身就属于 HTTP 协议，用于保留某个用户访问的信息，只保存在当前浏览器当中。
>
> 对于服务器而言，每一个连接到它的客户端都是一个 session，servlet 容器使用` javax.servlet.http.HttpSession`创建 HTTP 客户端和 HTTP 服务器之间的会话
>
> 简而言之，Session 的作用就是为了标识一次会话，或者说确认一个用户，并且在一次会话（一个用户的多次请求）期间共享数据

### Session的获取与基本使用

```
@WebServlet("/session1")
public class Session1 extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    	// 获取Session对象
        HttpSession session = request.getSession();

		// 获取Session会话标识符
		// D111F6C6F0A65C44184672B9BF04D728
        System.out.println(session.getId());

		// 获取Session创建时间
		// 1612756089930
        System.out.println(session.getCreationTime());

		// 获取最后一次访问时间
		// 1612756089935
        System.out.println(session.getLastAccessedTime());
		
		// 判断是否是新的Session对象
		// false
        System.out.println(session.isNew());
    }
}
```

### JSESSIONID标识符

> 使用一个叫JSESSIONID的特殊cookie来标识一次会话，Session 的底层依赖 Cookie 来实现

![](http://cdn.ziyedy.top/Servlet%E5%9F%BA%E7%A1%80%E6%80%BB%E7%BB%93/session.png)

### Session域对象

> Session 用来表示一次会话，在一次会话中数据是可以共享的，这时 session 作为域对象存在

```
// 向域对象中添加数据
setAttribute(name, value)

// 从域对象中获取数据
getAttribute(name)

// 从域对象中移除数据
removeAttribute(name)
```

### Session对象的销毁

#### 默认到期时间

> 由服务器设置产生，如Tomcat 中 session 默认的存活时间为30min，一旦客户端有操作，session 会重新计时

可以在 Tomcat 中的 conf 目录下的 web.xml 文件中进行修改

```
<session-config>
	<session-timeout>30</session-timeout>
</session-config>
```

#### 设置到期时间

```
// 设置session的最大不活动时间(单位为秒)
session.setMaxInactiveInterval(30); // 30秒

// 获取session的最大不活动时间
int time = session.getMaxInactiveInterval();
```

#### 立即失效

```
// 销毁session对象
session.invalidate();
```

### Servlet三大域对象

> Servlet的三大域对象：
>
> 1、request域对象；在一次请求中有效
>
> 2、session域对象；在一次会话中有效
>
> 3、servletContext域对象；在整个应用程序中有效，服务器关闭后失效



# 文件的上传与下载

## 文件上传

### 前台实现

> 表单提交类型选择post；表单类型选择`enctype="multipart/form-data"`

```
<form method="post" action="uploadServlet" enctype="multipart/form-data">
    文件：<input type="file" name="myfile">
    <button type="submit">提交</button>
</form>
```

### 后台实现

> 1、使用注解`@MultipartConfig()`标识文件上传
>
> 2、 Servlet 将 `multipart/form-data` 的 POST 请求封装成 Part，通过 Part 对上传的文件进行操作

```
@WebServlet("/uploadServlet")
@MultipartConfig()
public class UploadServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setCharacterEncoding("UTF-8");
        Part part = request.getPart("myfile");

        String cd = part.getHeader("Content-Disposition");
        String fileName = cd.substring(cd.lastIndexOf("=")+2, cd.length()-1);

        String realPath = request.getServletContext().getRealPath("/");
        part.write(realPath + fileName);	// 写入文件
    }
}
```



## 文件下载

### 超链接下载

```
<!-- 当超链接遇到浏览器不识别的资源时，会自动下载 -->
<a href="test.zip">超链接下载</a>

<!-- 通过download属性可进行下载 -->
<a href="test.zip" download>超链接下载</a>
```