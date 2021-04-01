---
title: Ubuntu下bug解决
date: 2021-01-20 23:39:29
tags:
	- Ubuntu
categories:
	- 问题解决
	- Ubuntu
fileName: ubuntu-debug
---

## 系统频繁卡死

### 问题描述

```
EXT4-fs error
```



### 问题解决

编辑配置文件为GRUB添加内核启动参数

```shell
sudo nano /etc/default/grub
```

添加参数

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash nvme_core.default_ps_max_latency_us=5500"
```

保存并更新

```
sudo update-grub 
```

### 详见

https://askubuntu.com/questions/905710/ext4-fs-error-after-ubuntu-17-04-upgrade/906105#906105



## 无法打开终端

### 问题解决

在设置中更改时间格式即可（莫名其妙的一个bug）

### 参考

https://askubuntu.com/questions/1040566/cant-open-terminal-in-ubuntu-18-04-after-upgrade-from-17-10



## dpkg安装报错

```
E: Sub-process /usr/bin/dpkg returned an error code (1)
```

该问题产生的原因通常是由于有dpkg安装包安装时中断等引起

该问题产生的通常解决方法可见：https://www.tecmint.com/sub-process-usr-bin-dpkg-returned-an-error-in-ubuntu/

比如安装向日葵时就会产生这种中断从而导致该问题，解决方法：

即为在`/etc/apt/source.list`中加入`deb http://cz.archive.ubuntu.com/ubuntu bionic main universe`