---
title: 1、Django基础内容
date: 2020-06-19 10:46:18
tags:
	- Python
categories:
	- Python
	- Django
fileName: django1
---

模型（Model）



视图（View）



模板（Template）





```
django-admin startproject my_project
```



```
python manage.py runserver
```



url指定name，方便重定向

```
url = reverse('index_two')
return redirect(url)
```





url的嵌套，使用include关键字

```
from django.conf.urls import include

url('', inclued('child.urls'))
```



视图

一个视图函数，简称视图，是一个python函数

接受一个请求，返回一个响应。响应可以是HTML内容、文本、图像，甚至是404、重定向等

在urls.py文件中加上要响应的url

```
urlpatterns = [
    url(r'^admin/', admin.site.urls),

    # 展示当前时间
    url(r'^time/$', views.now_time),
]
```

在views.py中加入相应的视图函数

```
# 返回一个展示时间的html
def now_time(request):
    """ 展示时间 """
    now = datetime.now()
    html = """
    <html>
        <head>
            <style type="text/css">
                body {{color: blue}}
            </style>
        </head>
        <body>
            now: {0}
        </body>
    </html>
    """.format(now)
    return HttpResponse(html)
```





视图中获取URL参数

视图中获取URL中的请求（GET/POST等）参数

```
# 输入URL
http://index/?par=20
# 视图编写，若找不到par参数则返回默认值10
def index(request):
	par = request.GET.get('par', 10)
	request.POST.get()
```



返回HTML信息

通过文件读取方式返回

```
def gg(request):
    """ 从html文件读取内容，并响应 """
    html = ''
    file_name = os.path.join(settings.BASE_DIR, 'templates', 'index.html')
    with open(file_name) as f:
        html = f.read()
    return HttpResponse(html)
```

直接使用render_to_response函数

```
from django.shortcuts import render_to_response

def index(request):
    return render_to_response('index.html')
```



内置错误处理视图

400 Bad Request

403 Forbidden

404 Not Found

500 Internal Server Error



重写内置错误处理视图

在urls.py中添加配置

```
handler500 = 'django_app.views.page_500'
```

在views.py中添加

```
def page_500(request):
	pass
```



请求对象Request

请求方式method（POST/GET....）

请求头信息META

REMOTE_ADDR——请求的IP地址

HTTP_USER_AGENT——用户请求终端信息

获取请求传递参数

GET、POST、COOKIES、FILES



响应对象

HttpResponse

status：查看状态码

content_type：设置响应类型



FileResponse



使用类重写视图

1、继承视图。django.views.generic.TemplateView

2、配置模板地址

3、配置URL



TemplateView原理

1、从项目主目录寻找模板文件

2、从app进行寻找

```
class ShowClassView(TemplateView):
    """ class视图 """
    template_name = 'class.html'
    
url(r'^show/class/$', views.ShowClassView.as_view(), name
```





模板：

模板具有一定的格式或骨架，可以动态的生成HTML



模板引擎决定以何种方式组织代码

DTL

Jinja2



渲染机制

1、从磁盘读取模板文件（get_template）

2、选择合适模板引擎（select_template）

3、将制定内容对模板进行渲染（render）

4、发送给浏览器显示





渲染python中的对象



```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    我制定的值为: { { p1 } }
</body>
</html>
```





```
url(r'^text/$', views.text_show)

def text_show(request):
    """ 变量在模板中的渲染 """
    p1 = 10
    return render(request, 'index.html', {
        'p1' : p1
    })
```



```
模板标签

{% %}

循环控制

{% for item in list %}

<li> </li>

{% endfor %}
```



```
条件控制

{% if A %}
	满足A
{% elif B %}
	满足B
{% else %}
	都不满足
{% endif %}
```



```
注释

{# 注释内容 #}
```



示例

```
def text_show(request):
    """ 变量在模板中的渲染 """
    list = ['北京', "上海", "广州", "深圳"]
    return render(request, 'index.html', {
        'list': list
    })
```



```
<ul>
    {% for item in list  %}
        <li>
            {{ item }}
        </li>
    {% endfor %}
</ul>
```



模板过滤器：对变量进行处理后再渲染

```
{{ value|filter_name:params }}
```



自定义过滤器

1、在app目录下新建包templatetags，并新建filter.py

2、定义过滤器

```
from django import template

register = template.Library()

def func(value):
    pass

# 注册过滤器
register.filter('func', func)
```

3、在模板中使用过滤器（）

```
{% load filter %}
{{ value|func }}
```



模板的抽象和继承

