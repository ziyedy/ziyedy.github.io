---
title: 1、Python基本数据类型
date: 2020-05-08 12:46:26
tags:
	- python
categories:
	- Python
	- 语言基础
fileName: python1
---

### 字符串

1、 单双引号的应用，即字符串中包含单引号时可以使用双引号进行包含，包含双引号时使用单引号进行包含，如下：
```
"a's hhh"
'a"s hhh'
```

2、 字符串的一些基本操作（拼接、搜索、替换），其中**替换操作必须要将str赋给一个新的变量**
```
str = str1 + str2	# 拼接字符串（直接使用 + 进行操作）
len(str) # 获取长度
str.find(string) # 返回搜索到的第一个index，没搜索到则返回-1
str = str.replace('A', 'B') # A替换B
```

3、 格式化字符串（使用.format语句），使用{}占位符进行
```
"{} {} gg".format("huangkai","is")
# huangkai is gg

# 带索引的格式化字符串
"{1} {0} gg".format("huangkai","is")
is huangkai gg

# f形式的格式化字符串
str1 = 'Curry'
str2 = 'James'
combi = f'{str1} and {str2}, NBA stars'

# 使用别名
str3 = “gg {s1}， {s2}”。format(s1=str1, s2 str2)
```

### 列表（list）

列表在内存中按照顺序存储

1、 列表反转与排序

```
list.reverse()	# 列表反转
list.sort() # 升序排列
list.sort(reverse=True) # 降序排列
```

2、 列表常用操作

```
list.append(new_element) # 在列表末尾添加元素
list.insert(index, new_element) # 在指定的index处加入一个元素，其后元素全部后移
list.remove(element) # 删除值为element的元素
list.pop(index) # 删除索引值为index的元素，无index则删除最后一个元素
list.clear() # 清空列表
```

4、 列表其他操作（区别于append）

```
list.extend([e1, e2]) # 将列表追加进list（区别于append）
```

5、 **列表的复制**，想将一个列表赋值给另一个列表必须要使用.copy方法，否则修改其中一个表另一个也会变，如下：
```
l1 = [1,2,3,4]
l2 = l1
print(id(l1))
print(id(l2))
```
运行结果可以发现l1与l2指向了同一块内存，因此改变l1，l2也会相应改变。而copy()方法会重新创建一个空列表再将原列表的元素放进去。
```
l2 = list.copy()
```

6、列表元素直接的对应操作，以相加为例，使用列表生成器如下。其中zip函数为将a、b两个列表中的元素一一组合。

```
a = [1,2,3,4]
b = [1,2,3,4]
c = [i+j for i, j in zip(a,b)]
########################################
[2, 4, 6, 8]
```



### 字典（dict）

键值对（key-value），可修改。Python中的散列值函数`hash(data)`，字典存储根据其哈希值存储。

1、 创建
```
dic = dict.fromkeys(['school number', 'bala1', 'bala2'])
print(dic)

# 输出
{'school number': None, 'bala1': None, 'bala2': None}
```

2、 取值
```
# 直接使用key获取value
dic[key]
# 使用get方法（可以为不存在的key赋默认值）
dic.get(key)
```

3、 遍历字典
```
# 方案1
for key in dic:
	v = dic[key]
	
# 方案2（返回每一个键值对）
for key,value in dic.items():
	
```

4、 字典常用操作
```
dic.update(k1 = new_value1, k2 = new_value2) # 对多个键值进行更新或新增
dic,pop(key) # 删除键值为key
dic.popitem() # 删除最后一个键值对，并返回该键值对的tuple
dic.clear() # 清空字典
dic.setdefault('key', 'value') # 设置默认值（如果没有该键值的话）

# 修改字典时下面三个值都会变
dic.keys()	# 返回所有键
dic.values() # 返回所有值
dic.items() # 返回所有键值对

# 格式化字符串，使用format_map
dic = {'name':'Curry','gg':'no'}
s = "姓名:{name},瞎写:{gg}".format_map(dic)
print(s)
# 输出
姓名:Curry,瞎写:no
```

### 元组（tuple）

创建后不允许修改，当列表等可变对象为元组元素时，可以修改该元素的值。


### 集合（Set）

元素是无序的、可变的、元素不能重复，在内存中是分散保存的（存储方式与字典相似），缺点，浪费内存空间

1、 初始化
```
# 空集合的创建
# 不能使用s = {}，这样默认创建的是字典
set1 = set()
```
2、 集合的常用运算
```
set1 = {1,2,3,4,5}
set2 = {4,5,6,7,8}

# 交集运算
set3 = set1.intersection(set2)	# 产生新集合
# intersection_update为更新原有集合
print(set3)
# 输出
{4, 5}

# 并集运算
set3 = set1.union(set2)
print(set3)
# 输出
{1, 2, 3, 4, 5, 6, 7, 8}

# 差集运算
set3 = set1.difference(set2) # 得到set1中有，set2中没有的元素 
print(set3)
# 输出
{1, 2, 3}

# 双向差集
set3 = set1.symmetric_difference(set2)
print(set3)
# 输出
{1, 2, 3, 6, 7, 8}
```

3、 集合间的关系操作
```
# 判断两个集合的元素是否完全相同
s1 = {1,2,3,4,5}
s2 = {5,4,3,2,1}
print(s1 == s2)
# 输出
True
```

```
# 子集判断
# 父集判断，同理。 .issuperset()
s1 = {1,2,3,4,5}
s2 = {5,4,3,2}
print(s2.issubset(s1))
# 输出
True
```

```
# 判断两个集合是否存在重复元素
# 返回False代表存在重复元素
s1 = {1,2,3,4,5}
s2 = {5,4,3,2}
print(s1.isdisjoint(s2))
# 输出
False
```

```
# 新增数据
set.add(element) # 一次添加一个元素
set.update(列表或元组) # 一次添加多个元素
# 删除元素
set.remove(element) # 删除不存在的元素会报错
set.discard(element) # 删除不存在的元素会直接忽略
```

### 常用的转换函数

```
list() # 转换为列表
tuple() # 转换为元组
join(), str() # 转换为字符串
```

### 生成器

1、 **列表生成器**，常用形式如下

[数据 循环语句 循环或判断语句]

```
l = [i**2 for i in range(10)]
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

l = [i**2 for i in range(10) if i%2 ==0]
# [0, 4, 16, 36, 64]
```

2、 字典生成器

```
list1 = ['A', 'B', 'C']
dic = {i:list1[i] for i in range(len(list1))}
# {0: 'A', 1: 'B', 2: 'C'}
```

3、 集合生成器（跟列表生成式类似）
```
set1 = {i for i in range(10)}
```