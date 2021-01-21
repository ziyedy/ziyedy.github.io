---
title: Ubuntu下bug解决
date: 2021-01-20 23:39:29
tags:
	- Ubuntu
categories:
	- 问题解决
	- Ubuntu
fileName:
---



```
EXT4-fs error
```

系统频繁死机

https://askubuntu.com/questions/905710/ext4-fs-error-after-ubuntu-17-04-upgrade/906105#906105







```
E: Sub-process /usr/bin/dpkg returned an error code (1)
```

该问题产生的原因通常是由于有dpkg安装包安装时中断等引起

该问题产生的通常解决方法可见：https://www.tecmint.com/sub-process-usr-bin-dpkg-returned-an-error-in-ubuntu/

比如安装向日葵时就会产生这种中断从而导致该问题，解决方法：

即为在`/etc/apt/source.list`中加入`deb http://cz.archive.ubuntu.com/ubuntu bionic main universe`