---
title: Blender物体渲染脚本
date: 2020-09-13 09:17:32
tags:
	- 插件开发
categories:
	- Python
fileName: blender-script-basic
---

## bpy模块

### 常用子模块

#### bpy.ops

> 对于操纵3D对象，最重要的两个类是`bpy.ops.object`和`bpy.ops.mesh`
>
> Object类包含用于同时操作多个选定对象的函数以及许多常规实用程序。Mesh类包含用于一次操作一个对象的顶点、边和面的函数

#### bpy.context

```
context（正文）是blender数据管理中的一个全局变量集合，它包含了目前被选择的和激活的（物体的两种属性状态）的物体的信息，还记录了当前物体的编辑模式
用于按各种状态条件访问 Blender 的对象和区域（如用户选定的对象），用于将我们指向对象组（即`bpy.context`类将生成对`bpy.data`类的数据块的引用）
```



```
# 访问选定对象的列表
bpy.context.selected_objects

# 访问目前激活的对象
bpy.context.active_object
```



#### bpy.data

> blender界面上所有可使用调节的对象内容（包括mesh模型、lamp灯光、camera摄像机，material材质等）都在python中以特定的类型存储起来
>
> 用于访问Blender的内部数据，包含确定对象形状和位置的所有数据

访问出现的对象

```
bpy.data.objects  #访问场景内普通对象列表，包括lamp、camera以及mesh模型的
bpy.data.scenes  #访问场景列表
bpy.data.materials  #访问材质对象列表
```

访问具体的对象

```
bpy.data.objects['Cube']  #访问名为`Cube`的对象
bpy.data.objects[1]  #访问列表中的第1个对象
```

访问对象属性

```
bpy.data.objects['Cube'].name #访问对象名称
bpy.data.objects['Cube'].location #访问对象的位置
```





#### bpy.app

```
常用子模块`bpy.app.handpers`，包含特殊函数，用于触发自定义函数以响应Blender中的事件
```



- 选择：可以同时选择一个、多个或零个对象。使用选定对象的操作可以同时对单个对象或多个对象执行该操作。
- 激活：在任何给定时间，只有一个对象可以处于活动状态。对活动对象执行操作的操作通常更具体、更激烈，因此无法同时直观地对许多操作执行。
- 规范：（仅限 Python）Python 脚本可以通过对象的名称访问对象，并直接写入其数据块。虽然操作选定对象的操作通常是一种差异操作，如平移、旋转或缩放，但将数据写入特定对象通常是一种声明性操作，如位置、方向或大小。





选择对象

```
# 选中所有
bpy.ops.object.select_all(action='SELECT')
# 取消所有选中
bpy.ops.object.select_all(action='DESELECT')

# 选中一个对象
bpy.data.objects['Cube'].select_set(True)
```





```

#通过select_pattern命令来按照物体命名查找选定物体，支持通配符*模糊匹配
bpy.ops.object.select_pattern(pattern="Cube", case_sensitive=False, extend=True)

# 选择名为Cube的立方体
bpy.data.objects['Cube']
```





激活对象

> 与选择不同，在任何给定时间只能有一个对象处于活动状态。此状态通常用于单个对象的顶点、边和面操纵



```
# 访问活动对象属性
bpy.context.object.name
```







改变选中物体的大小

```
# 按 x y z 进行缩放
bpy.ops.transform.resize(value=(1.1, 1.2, 1.4))
```



旋转改变物体

```
# 绕 Z轴 旋转 3.14 弧度
bpy.ops.transform.rotate(value=3.14, orient_axis='Z')
```



平移

```
# 按 x y z 方向平移物体
bpy.ops.transform.translate(value=(1, -2, 3))

# 设置位置
bpy.data.objects['Cube'].location = (1, 2, 3)
```





灯光

```
scene = bpy.context.scene
light_data = bpy.data.lights.new(name='Light', type='POINT')
lamp_object = bpy.data.objects.new(name="Light", object_data=light_data)
scene.objects.link(lamp_object)
bpy.context.view_layer.objects.active = lamp_object
```



相机操作









## bmesh模块























