---
title: Blender使用Python脚本的基本渲染操作
date: 2020-09-13 09:17:32
tags:
	- Blender
categories:
	- Python
top: true
fileName: blender-python-script-basic
---

> 渲染可以简单理解为将3D场景转化为2D场景的过程

## bpy模块

### bpy.ops

> 对于操纵3D对象，最重要的两个类是`bpy.ops.object`和`bpy.ops.mesh`
>
> Object类包含用于同时操作多个选定对象的函数以及许多常规实用程序。Mesh类包含用于一次操作一个对象的顶点、边和面的函数

### bpy.context

> context（正文）是blender数据管理中的一个全局变量集合，它包含了目前被选择的和激活的（物体的两种属性状态）的物体的信息，还记录了当前物体的编辑模式
> 用于按各种状态条件访问 Blender 的对象和区域（如用户选定的对象），用于将我们指向对象组（即`bpy.context`类将生成对`bpy.data`类的数据块的引用）

```
# 访问选定对象的列表
bpy.context.selected_objects

# 访问目前激活的对象
bpy.context.active_object
```

### bpy.data

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

## 常用基本操作

### 选择对象

```
# 选中所有
bpy.ops.object.select_all(action='SELECT')

# 取消所有选中
bpy.ops.object.select_all(action='DESELECT')

#通过select_pattern命令来按照物体命名查找选定物体，支持通配符*模糊匹配
bpy.ops.object.select_pattern(pattern="Cube", case_sensitive=False, extend=True)

# 选中名为Cube的对象
bpy.data.objects['Cube'].select_set(True)
```

### 对选中物体进行缩放

```
# 按 x y z 进行缩放
bpy.ops.transform.resize(value=(1.1, 1.2, 1.4))
```

### 对选中物体进行旋转

```
# 绕 Z轴 旋转 3.14 弧度
bpy.ops.transform.rotate(value=3.14, orient_axis='Z')
```

### 对选中物体进行平移操作

```
# 按 x y z 方向平移物体
bpy.ops.transform.translate(value=(1, -2, 3))

# 设置位置
bpy.data.objects['Cube'].location = (1, 2, 3)
```

## 灯光操作

```
# 新建一个点光源
light_data = bpy.data.lights.new(name='Light', type='POINT')

# 设置该光源的一些属性（具体属性可查看文档）
light_data.distance = 2400.
light_data.use_nodes = True
light_data.energy = 5

# 激活灯光
light = bpy.data.objects.new(name="Light", object_data=light_data)
scene.objects.link(light)
bpy.context.view_layer.objects.active = light

# 脚本使用完毕后删除灯光
bpy.data.objects['Light'+str(i)].select_set(True)
bpy.ops.object.delete()
```

## 相机操作

### 改变相机指向

```
def look_at(obj_camera, point=Vector((0, 0, 0))):
	"""
	使相机指向point点
	"""
    loc_camera = obj_camera.location
    direction = point - loc_camera
    rot_quat = direction.to_track_quat('-Z', 'Y')
    obj_camera.rotation_euler = rot_quat.to_euler()
```

## 实现多角度渲染物体

> 实现多角度渲染物体生成物体模板主要需要进行以下工作
>
> 1、合理设置相机的位置（以LineMOD方法为例就利用球面的几何特征设置相机位置）
>
> 2、合理布局灯光（调整使得灯光能够覆盖）
>
> 3、得到此时相机的Rt变换矩阵用于标识该角度下物体的姿态
>
> 4、输入模型，进行渲染输出图片

第1点和第2点其实就是之前的灯光操作与相机操作

### 进行模型渲染

#### 导入模型

以.obj文件为例，其余文件格式的导入方法可以查官方文档（导入的模型默认的位置为坐标原点）

```
# 导入.obj文件
bpy.ops.import_scene.obj(filepath=obj_path)
```

除导入模型外，就是合理设置输入路径和输出路径了，这个可以用os等模块进行处理

#### 设置渲染输出的基本信息

根据官网可以设置输出分辨率等基本信息

```
context = bpy.context
scene = context.scene

scene.render.engine = 'CYCLES'
scene.render.film_transparent = True
scene.render.resolution_x = 360
scene.render.resolution_y = 360
scene.render.image_settings.file_format = 'PNG'
```

#### 渲染输出

```
# 设置输出路径
scene.render.filepath = output_path + "0".zfill(5) + ".png"

# 渲染输出
bpy.ops.render.render(write_still=True, use_viewport=False)
```

#### 渲染结果

渲染结果如下，实现了**模型的批量渲染处理**

![](http://cdn.ziyedy.top/Blender%E7%89%A9%E4%BD%93%E6%B8%B2%E6%9F%93%E8%84%9A%E6%9C%AC/%E6%B8%B2%E6%9F%93%E7%BB%93%E6%9E%9C1.png)

![](http://cdn.ziyedy.top/Blender%E7%89%A9%E4%BD%93%E6%B8%B2%E6%9F%93%E8%84%9A%E6%9C%AC/%E6%B8%B2%E6%9F%93%E7%BB%93%E6%9E%9C2.png)

## Blender脚本编写tips

### 根据软件操作获得代码

对blender不熟悉的话很多操作不知道如何使用Python编写（官方文档是指望不上的），所以可以在软件内进行操作，然后在Script中即可以看到上次操作的代码，这样的话如果摸清楚了Blender中的操作流程的话，可以很方便的转化为代码

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%B8%B2%E6%9F%93%E6%93%8D%E4%BD%9C/blender%E6%98%BE%E7%A4%BA%E6%93%8D%E4%BD%9C%E4%BB%A3%E7%A0%81.png)

### IDE开发

bpy模块是blender脚本插件核心库，封装在blender内置的Python库中，因此在外界要使用该库并不方便

为了能够在相关IDE中进行blender二次开发，可以使用`fake-bpy-module`这个包，该包是一套Blender API伪SDK库，能够用于blender二次开发的相关代码补全

安装`fake-bpy-module`只需要使用pip就可以了，如下就是安装2.83版本的 方法

```
pip install fake-bpy-module-<version>
pip install fake-bpy-module-2.83
```

### 帮助文档

blender插件脚本的python开发是一个文档相对欠缺（官方文档写的我个人认为很烂）而且不同版本之间差别较大。

因此建议初学者不要直接上官方文档，而应该面对具体问题在 https://blender.stackexchange.com/ 上找到相应解决方案

或者可以面向软件，找到在软件中的操作手法，之后根据tip1完成代码的编写


