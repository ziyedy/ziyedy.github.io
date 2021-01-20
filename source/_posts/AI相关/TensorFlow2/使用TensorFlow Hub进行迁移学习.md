---
title: 使用TensorFlow Hub进行迁移学习
date: 2020-08-28 22:21:26
tags:
	- TensorFlow2
categories:
	- AI相关
	- TensorFlow2
fileName: TF-Hub-transfer-learning
---

使用[TensorFlow Hub](https://tfhub.dev/)中的模型能够很轻松的实现模型调用与迁移学习

```
import tensorflow_hub as hub

# 你所想使用的模型的链接
classifier_url ="https://tfhub.dev/google/tf2-preview/mobilenet_v2/classification/2"

# 构建特征处理器
feature_extractor_layer = hub.KerasLayer(classifier_url, input_shape=(28, 28, 3))

# 冻结特征提取层中的变量
feature_extractor_layer.trainable = False

# 构建自己的模型
classifier = tf.keras.Sequential([
	feature_extractor_layer,
	layers.Dense(image_data.num_classes)	# 全连接分类层
])

# 配置训练过程
model.compile(
  optimizer=tf.keras.optimizers.Adam(),
  loss=tf.keras.losses.CategoricalCrossentropy(from_logits=True),
  metrics=['acc'])



# 使用该模型进行预测
result = classifier.predict(input_data)
```



进行迁移学习，只修改最后的分类层



```
class CollectBatchStats(tf.keras.callbacks.Callback):
  def __init__(self):
    self.batch_losses = []
    self.batch_acc = []

  def on_train_batch_end(self, batch, logs=None):
    self.batch_losses.append(logs['loss'])
    self.batch_acc.append(logs['acc'])
    self.model.reset_metrics()
```



