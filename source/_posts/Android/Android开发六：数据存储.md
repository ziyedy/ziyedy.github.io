---
title: post
date: 2020-09-07 19:53:37
tags:
categories:
fileName:
---

## 文件存储

### 写操作

> 使用`openFileOutput()`方法，接收以下两个参数，返回一个`FileOutputStream`对象：
>
> 1、 文件名（不包括路径）
>
> 2、 文件操作模式（MODE_PRIVATE<默认，写入数据覆盖原文件> 和 MODE_APPEND<向后追加文件>）

### 读操作

> `openFileInput()`方法，传入文件名，得到一个`FileInputStream`对象



## SharedPreferences存储

> 使用`xml`文件存储数据