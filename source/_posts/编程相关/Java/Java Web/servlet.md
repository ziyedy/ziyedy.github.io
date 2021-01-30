---
title: servlet
date: 2021-01-28 13:03:39
tags:
categories:
fileName:
---

## Servlet基本概念

Servlet是用Java语言编写的运行在服务器端的小应用程序，用来完成B/S架构下，客户端请求的响应处理



Servlet容器

Servlet容器为JavaWeb应用提供运行时环境，它负责管理Servlet和JSP的生命周期，以及管理它们的共享数据，Tomcat



在整个Servlet程序之中最重要的就是Servlet接口，在此接口下定义了一个GenericServlet的子类，但是一般不会直接继承此类，而是根据所使用的协议选择GenericServlet的子类继承，例如：现在是采用HTTP协议处理的，所以一般而言当需要使用HTTP协议操作时用户自定义的Servlet类都要继承HttpServlet类。



### 

Servlet 3.0中Servlet配置，不再需要在web.xml中配置访问的URL，直接在Servlet类中添加注解配置

表示该，网页`root/gg/ss`下

```
@WebServlet("/gg/ss")
```



### 处理中文乱码

解决服务器返回页面中文乱码问题

```
response.setContentType("text/html;charset=UTF-8");
```

解决post方式请求表单参数中文乱码问题

```
request.setCharacterEncoding("UTF-8");
```

Tomcat9.0已经解决了get方式请求中文乱码问题，可以不需要做此配置。



### 处理表单输入

```
<form action="test" method="get">
    请输入：
    <input type="text" name="info" />
    <input type="submit" value="提交" />
</form>
```



```
@WebServlet("/test")    // 子目录

public class test extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse resp)throws ServletException, IOException {
        resp.setContentType("text/html;charset=UTF-8"); // 解决中文乱码
        String info = req.getParameter("info");
        PrintWriter out=resp.getWriter();
        out.println("<html><head><title>test</title></head>");
        out.println("<body><h1>"+info+"</h1>");
        out.println("</body></html");
        out.close();
    }
}
```



### 生命周期

init()

service()	在其中根据请求方式调用doGet和doPost方法

destroy()



### 使用Cookie

获取Cookie

```
String id = UUID.randomUUID().toString();
Cookie cookie = new Cookie("id", id);
cookie.setMaxAge(60*60);	// 设置Cookie有效期为一个小时
resp.addCookie(cookie);
```

得到Cookie

```
Cookie[] cookies = req.getCookies();
if (cookies != null) {
	for (Cookie c : cookies) {
		if (c.getName().equals("id")) {
			out.println("Cookie is:" + c.getValue());
		}
	}
}
```

