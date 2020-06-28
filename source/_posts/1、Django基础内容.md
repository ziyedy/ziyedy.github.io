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