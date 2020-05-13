---

title: 4、Python文件读写
date: 2020-05-10 21:03:43
tags:
categories:
	- Python
	- 基础知识
fileName: python4
---

模块属性

dir——列出对象的所有属性和方法

help——查看类、方法的帮助信息

__name___ ——模块的名称

file——文件全路径





文件读写

with 上下文管理器（到达语句末尾，将自动关闭文件）

```
with open("*.txt") as f
	pass
```

```
# 读取文件，可以输入读取几个字符
read()
# 读取一行数据，也可以读取参数
readline()
# 读取所有行，并返回列表
readlines()
```

```
# 向文件中写入内容
write()
# 输入一个列表，将列表内容分别写入
wirtelines([])
```

