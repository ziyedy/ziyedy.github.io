---
title: 使用plyfile库对.ply文件进行读写操作
date: 2020-10-04 11:04:48
tags:
	- 3D视觉
	- 点云
categories:
	- Python
fileName: plyfile
---

### 安装plyfile库

直接使用pip命令即可

```
pip install plyfile
```

### 进行*.ply文件读取

读取ply文件返回的是numpy数组，读取的方法也比较简单

```
# 读入ply文件
plydata = PlyData.read('tet.ply')

# 读取所有x坐标
plydata.elements[0].data['x']
plydata['vertex'].data['x']
plydata['vertex']['x']

# 读取第一个点的3d坐标
plydata.elements[0].data[0]
plydata['vertex'][0]
```

#### 读取函数

```
from plyfile import PlyData, PlyElement
def read_ply(filename):
    """ 读取坐标，返回值为 n * 3 """
    plydata = PlyData.read(filename)
    pc = plydata['vertex'].data
    pc_array = np.array([[x, y, z] for x,y,z in pc])
    return pc_array
```

### 进行*.ply文件写入

```
from plyfile import PlyData, PlyElement
def write_ply(save_path, points, text=True):
    """
    输入点云数据，并写入一个*.ply文件
    :param save_path: 文件路径 + *.ply
    :param points:
    :param text:
    :return: 
    """
    points = [(points[i, 0], points[i, 1], points[i, 2]) for i in range(points.shape[0])]
    vertex = np.array(points, dtype=[('x', 'f4'), ('y', 'f4'), ('z', 'f4')])
    el = PlyElement.describe(vertex, 'vertex', comments=['vertices'])
    PlyData([el], text=text).write(save_path)
```



### 参考

> 以上主要就是利用plyfile库进行点数据的读取，关于面数据之类的其实与点数据差别不大，重点在于对ply文件格式的认识

https://blog.csdn.net/phy12321/article/details/107373073

https://pypi.org/project/plyfile/