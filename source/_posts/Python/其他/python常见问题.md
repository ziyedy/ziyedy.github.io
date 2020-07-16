---
title: python常见问题
date: 2020-06-26 17:29:13
tags:
categories:
	- Python
fileName: python_method
---

### 生成requirements.txt

使用`pipreqs`生成requirements

```
# 安装
pip install pipreqs
# 在当前目录生成（若存在即覆盖）
pipreqs . --encoding=utf8 --force
```

使用requirement.txt安装依赖：

```\
pip install -r requirements.txt
```

