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

![](http://cdn.ziyedy.top/Blender%E7%89%A9%E4%BD%93%E6%B8%B2%E6%9F%93%E8%84%9A%E6%9C%AC/%E6%B8%B2%E6%9F%93%E7%BB%93%E6%9E%9C1.png)

![](http://cdn.ziyedy.top/Blender%E7%89%A9%E4%BD%93%E6%B8%B2%E6%9F%93%E8%84%9A%E6%9C%AC/%E6%B8%B2%E6%9F%93%E7%BB%93%E6%9E%9C2.png)



## 总结

总而言之，blender插件脚本的python开发是一个文档相对欠缺（官方文档写的我个人认为很烂）而且不同版本之间差别较大的二次开发工具。

如果没有特别的要求非得使用blender，我的建议是能不用就不用（尤其对于新手）。顺带一提，blender内置的Python脚本开发环境也极其糟糕，理论上vscode内是有相应的插件的，但bug较多我个人认为也很不好用。

之后如果还会使用blender的话我会写一个专题好好记录一下学习的过程（希望不会有这一天）

### 参考

https://github.com/zju3dv/pvnet-rendering













