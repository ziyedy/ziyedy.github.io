---
title: conda产生的bug处理
date: 2020-09-26 10:35:45
tags:
categories:
fileName:
---

### 没有写权限

#### 问题描述

```
EnvironmentNotWritableError: The current user does not have write permissions to the target environment.
  environment location: /opt/anaconda3
  uid: 1000
  gid: 1000
```

#### 解决方法

将anconda及其子文件赋予最高权限

```
sudo chmod 777 -R /opt/anaconda3/
```



### 下载源问题

#### 问题描述

![](http://cdn.ziyedy.top/conda%E4%BA%A7%E7%94%9F%E7%9A%84bug%E5%A4%84%E7%90%86/%E4%B8%8B%E8%BD%BD%E6%BA%90%E9%97%AE%E9%A2%98.png)

#### 解决方法

换源或者直接采用conda默认的源（以下换回conda的默认源）

```
conda config --remove-key channels
```

换成清华源或中科大源，将以下配置文件写入`~/.condarc`中

```
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
ssl_verify: true
```





### 异常关机导致的conda异常中断

#### 问题描述

![](http://cdn.ziyedy.top/conda%E4%BA%A7%E7%94%9F%E7%9A%84bug%E5%A4%84%E7%90%86/%E5%BC%82%E5%B8%B8%E5%85%B3%E6%9C%BA%E5%AF%BC%E8%87%B4%E7%9A%84conda%E5%BC%82%E5%B8%B8%E4%B8%AD%E6%96%AD.png)

#### 解决方法

将所有出问题的文件移除

```
rm -rf "出错文件路径"
```

