---
title: Blender使用Python脚本生成CV数据集
date: 2021-02-26 17:12:39
tags:
	- Blender
categories:
	- 编程相关
	- Python
top: true
fileName: blender-render-cv-dataset
---

## 获取相机内参、旋转平移等基本信息

参考：https://blender.stackexchange.com/questions/38009/3x4-camera-matrix-from-blender-camera

这个应该是使用blender渲染数据集并获取相应GT信息的标准方法与代码了。

### 获取相机内参

主要可以通过在blender中对相机对象及渲染方面的设置得到，

1. 定义相机类（以blender中的相机对象作为输入）

```python
class Camera:
    def __init__(self, camera):
        self.camera = camera
```

2. 获取相机内部传感器的模式与尺寸

```python
def get_sensor_size(sensor_fit, sensor_x, sensor_y):
    if sensor_fit == 'VERTICAL':
        return sensor_y
    return sensor_x


def get_sensor_fit(sensor_fit, size_x, size_y):
    if sensor_fit == 'AUTO':
        if size_x >= size_y:
            return 'HORIZONTAL'
        else:
            return 'VERTICAL'
    return sensor_fit
```

3. 计算得到相机内参

```python
def get_camera_intrinsic(self):
    camd = self.camera.data
    if camd.type != 'PERSP':
        raise ValueError('Non-perspective cameras not supported')
    scene = bpy.context.scene
    f_in_mm = camd.lens
    scale = scene.render.resolution_percentage / 100
    resolution_x_in_px = scale * scene.render.resolution_x
    resolution_y_in_px = scale * scene.render.resolution_y
    # 获取传感器的最长边
    sensor_size_in_mm = get_sensor_size(camd.sensor_fit, camd.sensor_width, camd.sensor_height)
    # 获取传感器模式
    sensor_fit = get_sensor_fit(
        camd.sensor_fit,
        scene.render.pixel_aspect_x * resolution_x_in_px,
        scene.render.pixel_aspect_y * resolution_y_in_px
    )
    pixel_aspect_ratio = scene.render.pixel_aspect_y / scene.render.pixel_aspect_x
    if sensor_fit == 'HORIZONTAL':
        view_fac_in_px = resolution_x_in_px
    else:
        view_fac_in_px = pixel_aspect_ratio * resolution_y_in_px
    pixel_size_mm_per_px = sensor_size_in_mm / f_in_mm / view_fac_in_px
    s_u = 1 / pixel_size_mm_per_px
    s_v = 1 / pixel_size_mm_per_px / pixel_aspect_ratio

	# Parameters of intrinsic calibration matrix K
    u_0 = resolution_x_in_px / 2 - camd.shift_x * view_fac_in_px
    v_0 = resolution_y_in_px / 2 + camd.shift_y * view_fac_in_px / pixel_aspect_ratio
    skew = 0  # only use rectangular pixels

    K = Matrix(
        ((s_u, skew, u_0),
        (0, s_v, v_0),
        (0, 0, 1)))
    return K
```

4. 根据获取相机内参中的逻辑可以编写通过内参矩阵设置相机内参的代码

```python
def set_camera_intrinsic(self, K):
    scene = bpy.context.scene
    camd = self.camera.data

    scale = scene.render.resolution_percentage / 100
    resolution_x_in_px = scale * scene.render.resolution_x
    resolution_y_in_px = scale * scene.render.resolution_y

    # 获取传感器的最长边
    sensor_size_in_mm = get_sensor_size(camd.sensor_fit, camd.sensor_width, camd.sensor_height)
    # 获取传感器模式
    sensor_fit = get_sensor_fit(
        camd.sensor_fit,
        scene.render.pixel_aspect_x * resolution_x_in_px,
        scene.render.pixel_aspect_y * resolution_y_in_px
    )
    pixel_aspect_ratio = scene.render.pixel_aspect_y / scene.render.pixel_aspect_x
    if sensor_fit == 'HORIZONTAL':
        view_fac_in_px = resolution_x_in_px
        else:
            view_fac_in_px = pixel_aspect_ratio * resolution_y_in_px

            camd.lens = K[0, 0] * sensor_size_in_mm / view_fac_in_px
            camd.shift_x = (resolution_x_in_px / 2 - K[0, 2]) / view_fac_in_px
            camd.shift_y = ((K[1, 2] - resolution_y_in_px / 2) * pixel_aspect_ratio) / view_fac_in_px
```

### 获取物体旋转平移矩阵

主要在于获取相机在世界坐标系的位置（经过转化后可以视为blender中内置的坐标）与旋转，以及目标物体在世界坐标系中的位置与旋转，之后可以根据定义计算得到。

1. 获取相机的外参矩阵（代码逻辑可以参考上述stackexchange中的内容）

```python
def get_camera_extrinsic(self):
    R_bcam2cv = Matrix(
        ((1, 0, 0),
         (0, -1, 0),
         (0, 0, -1)))

    location, rotation = self.camera.matrix_world.decompose()[0: 2]

    R_world2bcam = rotation.to_matrix().transposed()
    T_world2bcam = -1 * R_world2bcam @ location

    R_world2cv = R_bcam2cv @ R_world2bcam
    T_world2cv = R_bcam2cv @ T_world2bcam

    RT = Matrix((
        R_world2cv[0][:] + (T_world2cv[0],),
        R_world2cv[1][:] + (T_world2cv[1],),
        R_world2cv[2][:] + (T_world2cv[2],),
        (0, 0, 0, 1)
    ))

    return RT
```

2. 获取物体相对于blender原点的旋转与平移

```python
transform_matrix = obj.matrix_world.copy()	// obj为获得的物体
gt_rt = camera_param['RT'] @ obj_matrix		// 相机外参与获取物体相乘
```

### 注意事项

1、当物体相对原点有旋转与平移的情况

obj与ply的存储坐标有不同（没有详细查看文件格式，但经过实验应该是这样的，如下图所示，即obj在读入blender中时会在x轴自带90度的旋转）

所以如果使用obj文件进行渲染时，在获取目标平移旋转矩阵时不进行以上第2步，则不能得到正确的GT值，需要注意。

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%94%9F%E6%88%90CV%E6%95%B0%E6%8D%AE%E9%9B%86/obj_and_ply.png)

2、对物体进行刚体运动后`matrix_world`没变

这是由于blender没有在物体转变后立刻重新计算，在blender2.8的版本下需要在刚体变化后加上如下代码，参考https://blender.stackexchange.com/questions/27667/incorrect-matrix-world-after-transformation

```python
context.view_layer.update()
```



## 渲染背景图片

即让对应的模型带上背景图片，参考https://henryegloff.com/how-to-render-a-background-image-in-blender-2-8/

在blender中的处理流程如下（在`Compositor`模式下）

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%94%9F%E6%88%90CV%E6%95%B0%E6%8D%AE%E9%9B%86/bg_render.png)

### 代码实现

1. 获取作为背景的图片

```python
bg_img = bpy.data.images.load(filepath="/path/to/bg_img.png")
```

2. 传入背景图片，使用脚本完成节点的构建

```python
def render_rgb(bg_img):
    scene = bpy.context.scene
    scene.use_nodes = True
    tree = scene.node_tree
    links = tree.links
    links.clear()

    # 合成渲染
    composite_node = tree.nodes["Composite"]
    rl_node = tree.nodes["Render Layers"]

    img_node = tree.nodes.new(type="CompositorNodeImage")
    scale_node = tree.nodes.new(type="CompositorNodeScale")
    ao_node = tree.nodes.new(type="CompositorNodeAlphaOver")

    img_node.image = bg_img

    scale_node.space = 'RENDER_SIZE'
    scale_node.frame_method = 'CROP'

    links.new(img_node.outputs['Image'], scale_node.inputs['Image'])
    links.new(scale_node.outputs['Image'], ao_node.inputs[1])
    links.new(rl_node.outputs['Image'], ao_node.inputs[2])

    links.new(ao_node.outputs['Image'], composite_node.inputs['Image'])

    scene.render.filepath = cfg.OUTPUT_DIR + 'test.png'
```



### 为物体加上阴影

添加plane在需要投影的位置并且合理设置灯光即可得到物体相对的投影，参考

```python
bpy.ops.mesh.primitive_plane_add()	// 新建plane
bpy.context.object.cycles.is_shadow_catcher = True	// 设置相应的渲染属性
```

### 渲染结果

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%94%9F%E6%88%90CV%E6%95%B0%E6%8D%AE%E9%9B%86/background.png)





## 生成深度图

在blender中的处理流程如下（在`Compositor`模式下），其实就是在渲染背景图的基础上新添加了一个用于处理depth的节点与一个渲染输出节点

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%94%9F%E6%88%90CV%E6%95%B0%E6%8D%AE%E9%9B%86/depth_render.png)

### 代码实现

1. 设置节点及连接渲染得到exr格式的深度图

```python
def render_depth():
    scene = bpy.context.scene
    tree = scene.node_tree
    links = tree.links

    rl_node = tree.nodes["Render Layers"]

    map_node = tree.nodes.new(type="CompositorNodeMapRange")
    map_node.inputs[1].default_value = 0
    map_node.inputs[2].default_value = 2
    map_node.inputs[3].default_value = 0
    map_node.inputs[4].default_value = 1

    depth_file_output = tree.nodes.new(type="CompositorNodeOutputFile")
    depth_file_output.base_path = ''
    depth_file_output.format.file_format = 'OPEN_EXR'
    depth_file_output.format.color_depth = '32'

    links.new(rl_node.outputs['Depth'], map_node.inputs[0])
    links.new(map_node.outputs[0], depth_file_output.inputs[0])

    depth_file_output.file_slots[0].path = cfg.OUTPUT_DIR + 'depth_'
```

2. 将渲染得到的exr格式的深度图转化为常用的png格式，需要安装`OpenEXR`库

```python
// 先转化为png再删除exr文件
def exr_to_png(exr_path):
    depth_path = exr_path.replace('.exr', '.png')
    exr_image = OpenEXR.InputFile(exr_path)
    dw = exr_image.header()['dataWindow']
    (width, height) = (dw.max.x - dw.min.x + 1, dw.max.y - dw.min.y + 1)

    def read_exr(s, width, height):
        mat = np.fromstring(s, dtype=np.float32)
        mat = mat.reshape(height, width)
        return mat

    dmap, _, _ = [read_exr(s, width, height) for s in
                  exr_image.channels('BGR', Imath.PixelType(Imath.PixelType.FLOAT))]
    dmap = Image.fromarray((dmap != 1).astype(np.int32))
    dmap.save(depth_path)
    exr_image.close()
    os.system('rm {}'.format(exr_path))
```





## 生成Mask

生成mask关键在于删除物体已有材质，之后将物体的材质设置成某颜色（或标准的数值），参考https://blender.stackexchange.com/questions/80906/create-a-segmentation-picture-with-each-object-class-rendered-in-different-color/80925#80925

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%94%9F%E6%88%90CV%E6%95%B0%E6%8D%AE%E9%9B%86/mask_render.png)

### 代码实现

```python
def render_mask():
    scene = bpy.context.scene
    objs = [obj for obj in bpy.data.objects if obj.type in ("MESH", "CURVE")]
    for obj_idx, obj in enumerate(objs):
        color = cfg.colors[obj_idx] + (1,)	# 得到不同的颜色

        material_name = "auto.material." + obj.name	
        material = bpy.data.materials.new(material_name)

        material["is_auto"] = True
        material.use_nodes = True
        material.node_tree.nodes.clear()

        emission = material.node_tree.nodes.new(type="ShaderNodeEmission")
        emission.inputs['Color'].default_value = color	# 给不同物体设置不同颜色

        output = material.node_tree.nodes.new(type="ShaderNodeOutputMaterial")
        material.node_tree.links.new(emission.outputs['Emission'], output.inputs['Surface'])

        obj.data.materials.clear()	# 删除物体之前的所有材质
        obj.data.materials.append(material)	# 将刚刚创建并初始化颜色的材质加入物体

    scene.render.filepath = cfg.OUTPUT_DIR + 'mask.png'
```

### 渲染结果

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%94%9F%E6%88%90CV%E6%95%B0%E6%8D%AE%E9%9B%86/mask.png)



## 为模型中每个点赋值并渲染

由于渲染数据集时需要使用新数据集用nocs方法进行方法性能检验，所以必须要渲染得到nocs图（nocs相关见https://github.com/hughw19/NOCS_CVPR2019）

参考https://blender.stackexchange.com/questions/2324/using-wrl-data-in-cycles/2325#2325

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%94%9F%E6%88%90CV%E6%95%B0%E6%8D%AE%E9%9B%86/nocs_render.png)

### 代码实现

```python
def render_nocs():
    scene = bpy.context.scene
    objs = [obj for obj in bpy.data.objects if obj.type in ("MESH", "CURVE")]
    for obj_idx, obj in enumerate(objs):
        vcol_layer = obj.data.vertex_colors.new()
        for loop_index, loop in enumerate(obj.data.loops):
            loop_vert_index = loop.vertex_index
            scale = 1
            color = scale * obj.data.vertices[loop_vert_index].co + Vector([0.5, 0.5, 0.5])
            color = tuple(color) + (1,)

            vcol_layer.data[loop_index].color = color
        obj.data.vertex_colors.active = vcol_layer
        obj.data.update()

        material = bpy.data.materials.new('coord_color')

        material.alpha_threshold = 1

        material["is_auto"] = True
        material.use_nodes = True
        material.node_tree.nodes.clear()

        attr = material.node_tree.nodes.new(type="ShaderNodeAttribute")
        attr.attribute_name = "Col"

        output = material.node_tree.nodes.new(type="ShaderNodeOutputMaterial")

        material.node_tree.links.new(attr.outputs['Color'], output.inputs['Surface'])

        obj.data.materials.clear()
        obj.data.materials.append(material)
        obj.active_material = material
    scene.render.filepath = cfg.OUTPUT_DIR + 'nocs.png'
```

### 渲染结果

![](http://cdn.ziyedy.top/Blender%E4%BD%BF%E7%94%A8Python%E8%84%9A%E6%9C%AC%E7%94%9F%E6%88%90CV%E6%95%B0%E6%8D%AE%E9%9B%86/nocs.png)

## 不错的源码

blender中资源本身比较少，以下是我编写代码时找到的一个质量不错的源码，值得参考

https://github.com/DIYer22/bpycv