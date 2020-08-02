---
title: post
date: 2020-08-01 17:33:46
tags:
categories:
fileName:
---

 

候选框

直接回归得到位置



One-stage（YOLO和SSD系列）





Two-stage（RCNN、 Fast RCNN 系列）

准确度高、速度相对慢

（RPN网络）





proposal





RPN网络：

区域推荐（Anchor机制）：锚点，候选区域中心点  是否包含目标（>0.7 正样本， <0.3 负样本）

ROI pooling：抠图+resize

分类回归





One stage

使用CNN

直接回归物体的类别概率和位置坐标值（无region proposal）

相对而言，准确度低，速度相对较快



1、区域回归

2、Anchor机制





评价：





COCO数据集

物体检测