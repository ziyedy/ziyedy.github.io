---
title: conda产生的bug处理
date: 2020-09-26 10:35:45
tags:
	- conda
categories:
	- 问题解决
fileName: conda-debug
---

## 没有写权限

### 问题描述

```
EnvironmentNotWritableError: The current user does not have write permissions to the target environment.
  environment location: /opt/anaconda3
  uid: 1000
  gid: 1000
```

### 解决方法

将anconda及其子文件赋予最高权限

```
sudo chmod 777 -R /opt/anaconda3/
```



## 下载源问题

### 问题描述

![](http://cdn.ziyedy.top/conda%E4%BA%A7%E7%94%9F%E7%9A%84bug%E5%A4%84%E7%90%86/%E4%B8%8B%E8%BD%BD%E6%BA%90%E9%97%AE%E9%A2%98.png)

### 解决方法

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





## 异常关机导致的conda异常中断

### 问题描述

![](http://cdn.ziyedy.top/conda%E4%BA%A7%E7%94%9F%E7%9A%84bug%E5%A4%84%E7%90%86/%E5%BC%82%E5%B8%B8%E5%85%B3%E6%9C%BA%E5%AF%BC%E8%87%B4%E7%9A%84conda%E5%BC%82%E5%B8%B8%E4%B8%AD%E6%96%AD.png)

### 解决方法

将所有出问题的文件移除

```
rm -rf "出错文件路径"
```



## 创建新环境报错

### 报错信息

```
Collecting package metadata (current_repodata.json): failed

NotWritableError: The current user does not have write permissions to a required path.
  path: /home/ziyedy/.conda/pkgs/urls.txt
  uid: 1000
  gid: 1000

If you feel that permissions on this path are set incorrectly, you can manually
change them by executing

  $ sudo chown 1000:1000 /home/ziyedy/.conda/pkgs/urls.txt

In general, it's not advisable to use 'sudo conda'.

```

### 解决方案

按照提示尝试操作没用，解决方法是命令行输入如下：

```
sudo chown -R ziyedy anaconda3 # ziyedy is user name.
```

参考：https://blog.csdn.net/PecoHe/article/details/104578700



## 无法改变环境

正常情况下输入`conda activate <env_name>`时，python应该改变为该环境下的python版本，可以使用`python -V`查看，但有时命令行显示上环境已经更改但实际上所使用的python解释器没有更换，解决方法如下：

1. 使用记事本打开编辑环境（该目录下的.bashrc存储的为个人的一些配置文件，如别名路径）

```
sudo gedit ~/.bashrc
```

2. 在末尾按照规律添加如下内容

```
export PATH="export PATH="~/anaconda3/bin:$PATH"   #base环境下的python，已有的
alias python1="~/anaconda3/bin/python   # 你需要添加的
```

`alias`用于设置指令的别名，下面一条语句的意思就算你输入python1就会自动指向后面

3. 使得修改生效

```
source ~/.bashrc
```

