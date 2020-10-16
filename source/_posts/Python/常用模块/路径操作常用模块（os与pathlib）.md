---
title: 路径操作常用模块（os与pathlib）
date: 2020-06-20 09:41:07
tags:
categories:
	- Python
	- 常用模块
fileName: Python-path-operation
---

## OS模块

### 模块简介

> os模块是Python标准库中的一个用于**访问操作系统功能**的模块。使用os模块中提供的接口可以实现跨平台访问。



### 系统操作

os.sep：用于系统路径的分隔符。Windows为“\\\”，Linux为“/”。

os.name：指示正在使用的工作平台。Windows为‘nt’，Linux为‘posix’。

os.getenv('path')：读取环境变量名称

os.getcwd()：获取当前路径

os.getpid()：获取当前进程ID

#### os.system

> 将字符串转化为命令行在操作系统上执行。每次执行system函数，会创建一个子进程在系统上执行命令（**子进程的执行结果无法影响主进程**）





### 目录操作

**os.listdir(path)**：列出指定目录下的所有文件和子目录，包括隐藏文件；参数为空，为当前目录，返回一个列表。

os.mkdir()：创建一个目录。

os.rmdir()：删除一个空目录，如果该目录中有文件则删除不了。



**os.rename(src, dst) **：用于重命名文件或目录，从src改为dst。若运行出错会抛出OSError。



### Path模块

os.path.exists(path)：判断文件或者目录是否存在。存在返回True，不存在返回False。

os.path.isfile(path)：判断是否为文件。是返回True，不是返回False。

os.path.isdir(path)：判断是否为目录。是返回True，不是返回False。



os.path.abspath()：返回绝对路径

os.path.realpath(\__file__)：获取当前.py文件的目录

os.path.dirname()：返回文件路径

os.path.basename(path) ：返回文件名



os.path.split(path) ：拆分路径dirname和basename，返回为元组

**os.path.splitext('')**：将文件名与后缀名分开



**os.path.join(path, name)**：连接目录与文件名或目录



#### 示例

```
import os

file_path = os.path.realpath(__file__)  # 该py文件目录
print(file_path)
print(os.path.dirname(file_path))   # 上级目录
print(os.path.basename(file_path))  # py文件名称
print(os.path.split(file_path)) # 分割目录与名称

##############################################
C:\Users\lenovo\Desktop\vega\gg.py
C:\Users\lenovo\Desktop\vega
gg.py
('C:\\Users\\lenovo\\Desktop\\vega', 'gg.py')
```



### 操作实例

1、将某一路径下的文件名称中的广告部分去除并填上文件后缀名

```
def rename():
    path = '你的路径'
    file_list = os.listdir(path)	# 得到文件名列表
    for file in file_list:
        old_dir = os.path.join(path, file)	#得到初始路径
        # 若该文件名为路径，则继续遍历
        if os.path.isdir(old_dir):
            continue
        file_name = os.path.splitext(file)[0]
        # 在该处进行文件名的字符串更改操作
        file_type = os.path.splitext(file)[1]
        # 在该处变更文件的后缀
        file_name = file_name+file_type
        new_dir = os.path.join(path, file_name)	# 新的路径
        os.rename(old_dir, new_dir)	# 完成文件的重命名
```



## pathlib模块

### 基础使用

#### 导入

```
from pathlib import Path
```

#### 列出子目录

```
In[5]: p = Path('C:\\Users\\lenovo\\.keras\\datasets\\flower_photos')
In[6]: [x for x in p.iterdir() if x.is_dir()]
Out[6]: 
[WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/daisy'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/dandelion'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/roses'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/sunflowers'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/tulips')]
```

#### 获取当前目录下所有文件与文件夹

```
In[7]: list(p.glob('*/'))
Out[7]: 
[WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/daisy'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/dandelion'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/LICENSE.txt'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/roses'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/sunflowers'),
 WindowsPath('C:/Users/lenovo/.keras/datasets/flower_photos/tulips')]
```

#### 路径拼接

```
In[9]: q = p / 'daisy'
In[10]: print(q)
C:\Users\lenovo\.keras\datasets\flower_photos\daisy
```

#### 查询属性

路径是否存在与是否是路径

```
In[11]: q.exists()
Out[11]: True
In[12]: q.is_dir()
Out[12]: True
```





### 参考链接

https://docs.python.org/zh-cn/3/library/pathlib.html