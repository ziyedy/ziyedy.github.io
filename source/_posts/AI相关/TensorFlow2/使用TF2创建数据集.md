---
title: 使用TF2创建数据集
date: 2020-08-25 16:31:59
tags:
	- TensorFlow2
categories:
	- AI相关
	- TensorFlow2
fileName: tf2-create-dataset
---

### 使用数据集

本例中使用的数据集分布在图片文件夹中，一个文件夹含有一类图片。数据集存储格式如下

```
flower_photo/
  daisy/
  dandelion/
  roses/
  sunflowers/
  tulips/
```



### 下载并查看数据集

#### 获取数据根目录

```
import pathlib
data_url = 'https://storage.googleapis.com/download.tensorflow.org/example_images/flower_photos.tgz'
data_root_orig = tf.keras.utils.get_file(origin=data_url,
                                         fname='flower_photos', 
                                         untar=True)
data_root = pathlib.Path(data_root_orig)	# data_root存储数据的文件夹的根目录
```

#### 获取所有图片路径并打乱

```python
all_image_paths = list(data_root.glob('*/*.jpg'))	## 获取所有图片路径
all_image_paths = [str(path) for path in all_image_paths]	# 转化为str
random.shuffle(all_image_paths)	# 打乱
image_count = len(all_image_paths)	# 获取图片个数：3670

print(all_image_paths[99])	# 打印第100张图的路径
# C:\Users\lenovo\.keras\datasets\flower_photos\tulips\5704726114_a92f753514.jpg
```

#### 获取图片标签信息

```python
label_names = sorted(item.name for item in data_root.glob('*/') if item.is_dir())	# 根据文件夹名获取标签名
label_to_index = dict((name, index) for index, name in enumerate(label_names))	# 从0开始给标签设置索引
all_image_labels = [label_to_index[pathlib.Path(path).parent.name]
                    for path in all_image_paths]	# 将每张图片与标签一一对应

print(label_names)	# ['daisy', 'dandelion', 'roses', 'sunflowers', 'tulips']
print(label_to_index)	# {'daisy': 0, 'dandelion': 1, 'roses': 2, 'sunflowers': 3, 'tulips': 4}
print(all_image_labels[99])	# 打印第100张图的标签：4
```

由此可见，已经完成了图片与标签一一对应的关系

#### 可视化图片与标签

```
import matplotlib.pyplot as plt
for i in range(200, 209):
    plt.subplot(3, 3, i-199)
    img = plt.imread(all_image_paths[i])
    plt.imshow(img)
    plt.xlabel(label_names[all_image_labels[i]])
plt.show()
```

![](http://cdn.ziyedy.top/%E4%BD%BF%E7%94%A8TF2%E5%88%9B%E5%BB%BA%E6%95%B0%E6%8D%AE%E9%9B%86/%E5%8F%AF%E8%A7%86%E5%8C%96%E5%9B%BE%E7%89%87%E4%B8%8E%E6%A0%87%E7%AD%BE.png)



### 加载和格式化图片

```python
def load_and_preprocess_image(path):
	""" 输入为图片路径列表，输出为图片处理后的列表 """
    images = []
    for i in range(len(path)):
        url = path[i]
        image = tf.io.read_file(url)
        image = tf.image.decode_jpeg(image, channels=3)
        image = tf.image.resize(image, [192, 192])
        image = image / 255.0
        images.append(image)
    return images
```

![处理后数据可视化](http://cdn.ziyedy.top/%E4%BD%BF%E7%94%A8TF2%E5%88%9B%E5%BB%BA%E6%95%B0%E6%8D%AE%E9%9B%86/%E5%A4%84%E7%90%86%E5%90%8E%E6%95%B0%E6%8D%AE%E5%B1%95%E7%A4%BA.png)



### 构建数据集

#### tf.data.Dataset的基本使用

tf.data.Dataset是TF2中专门用于数据输入的接口，可以高效的实现数据的读入、打乱（shuffle）、增强（augment）等功能，以下示例简单介绍该接口使用方法

```
data = np.array([0.1, 0.2, 0.4, 0.05])
label = np.array([0, 1, 1, 0])
dataset = tf.data.Dataset.from_tensor_slices((data, label))	# 组合为数据集

# 遍历打印
# tf.Tensor(0.1, shape=(), dtype=float64) tf.Tensor(0, shape=(), dtype=int32)
# tf.Tensor(0.2, shape=(), dtype=float64) tf.Tensor(1, shape=(), dtype=int32)
# tf.Tensor(0.4, shape=(), dtype=float64) tf.Tensor(1, shape=(), dtype=int32)
# tf.Tensor(0.05, shape=(), dtype=float64) tf.Tensor(0, shape=(), dtype=int32)

dataset = dataset.shuffle(buffer_size=image_count)	# 打乱数据
dataset = dataset.repeat()	# 使数据能够重复使用
dataset = dataset.batch(batch_size=32)	# 每一个batch为32

# 设置batch后遍历打印
# tf.Tensor([0.1 0.2], shape=(2,), dtype=float64) tf.Tensor([0 1], shape=(2,), dtype=int32)
# tf.Tensor([0.4  0.05], shape=(2,), dtype=float64) tf.Tensor([1 0], shape=(2,), dtype=int32)
```

**使用map函数**

```
def func(data, label):
    data = data * 100
    return data, label

dataset = dataset.map(func)

# tf.Tensor(10.0, shape=(), dtype=float64) tf.Tensor(0, shape=(), dtype=int32)
# tf.Tensor(20.0, shape=(), dtype=float64) tf.Tensor(1, shape=(), dtype=int32)
# tf.Tensor(40.0, shape=(), dtype=float64) tf.Tensor(1, shape=(), dtype=int32)
# tf.Tensor(5.0, shape=(), dtype=float64) tf.Tensor(0, shape=(), dtype=int32)
```

**处理data的map函数的通用格式：**

```
def func(data, label):
	# 对data进行处理
	return data, label
```



### 本次构建数据集

```
dataset = tf.data.Dataset.from_tensor_slices((load_and_preprocess_image(all_image_paths), all_image_labels))
dataset = dataset.shuffle(buffer_size=image_count)
dataset = dataset.repeat()
dataset = dataset.batch(batch_size=32)
```



### 进行训练

之后按照通用的构建网络的模板进行训练即可（构建网络的过程略）

```
history = model.fit(dataset, epochs=10, steps_per_epoch=115)
```



### 参考链接

https://www.tensorflow.org/tutorials/load_data/images?hl=zh-cn