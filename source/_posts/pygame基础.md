---
title: pygame基础
date: 2020-05-15 16:11:20
tags:
	- pygame
categories:
	- Python
	- pygame
fileName: pygame1
---

官方文档：https://www.pygame.org/docs/

### 基本流程

1. 引入相关的包
2. 初始化，pygame.init()
3. 得到屏幕对象，`screen = pygame.display.set_mode((320, 240))`
4. 游戏主循环
   * 处理游戏事件
   * 更新游戏状态
   * 屏幕重新绘制

基本代码框架

```
import pygame
# 初始化
pygame.init()
# 获取屏幕对象
screen = pygame.display.set_mode((width, height))
# 游戏主循环
while True:
	# 处理游戏事件
	for event in pygame.event.get():
		pass
	# 更新游戏状态（游戏逻辑）
	
	# 重新绘制屏幕
```



### 绘制图片

基本流程如下

```
# 加载图片
ball = pygame.image.load('assets/images/background.png')

# 图片绘制
    screen.blit(ball, ball.get_rect)
    pygame.display.flip()
```

其中`get_rect`返回值为（left, top, width, height）。前者为图片显示的左上角坐标，后者为图片的长与宽。

绘制其他模块自带的图形

```
red = pygame.Color(255, 0, 0)
# 画线
pygame.draw.line(screen, red, (10,10), (200, 200),10)
# 画矩形
pygame.draw.rect(screen,red,(10,20,200,300),10)
# 画圆
pygame.draw.circle(screen, red, (100, 100),50,5)

```



### 绘制文字

可以使用系统默认的字体进行加载，也可以在项目中使用ttf文件作为资源进行加载。

```
# 加载字体（使用系统默认字体进行加载）
red = pygame.Color(255, 0, 0)
fonts = pygame.font.SysFont('宋体', 40)
text = fonts.render('Scores:', True, red)

	screen.blit(text, (20, 20))
    pygame.display.flip()
```

加载项目中所带的ttf文件

```
fonts = pygame.font.Font('*.ttf', 40)
```



### 游戏音效

```
# 加载音乐
bgm = pygame.mixer_music.load('assets/sounds/game_bg_music.mp3')
# 循环播放
pygame.mixer_music.play(-1)
```

设置音量大小

```
pygame.mixer_music.set_volume(0.1) 输入值为（0，1）
```



### 动画切换

两张图片的切换（pygame貌似不支持使用gif之类的动图）

思路：通过设置帧率进行切换，`pygame.time.Clock`

```
import sys, pygame
# 初始化
pygame.init()
# 屏幕对象
screen = pygame.display.set_mode((320, 800))

image1 = pygame.image.load('assets/images/hero1.png')
image2 = pygame.image.load('assets/images/hero2.png')
count = 0
clock = pygame.time.Clock()

# 游戏主循环
while True:

    count += 1
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
    screen.fill(pygame.Color(255, 255, 255))
    # 每秒60次
    clock.tick(60)
    if count % 5 == 0:
        screen.blit(image1, (20, 20))
    else:
        screen.blit(image2, (20, 20))

    pygame.display.update()
```



### 精灵及精灵组

**精灵**：可以理解为需要操纵的图片（飞机大战中的飞机），一种可以在屏幕上移动的图形对象，并且可以和其他图形对象交互。可以使用pygame绘制，也可以是资源图片。

**精灵组**：是一个容器，用于管理组内精灵的绘制与更新，精灵加入精灵组，精灵组会自动调用每一个精灵的方法。

基本使用方法就是，对自己的精灵类继承自`pygame.sprite.Sprite`，然后填补update之类的方法即可。

**碰撞检测**：检测两个精灵有没有重叠的部分

```
# 输入两个精灵，输出为布尔值
res = pygame.sprite.collide_rect(sprite1, sprite2)
# 指定碰撞精度
res = pygame.sprite.collide_rect_ratio(0.5)(sprite1, sprite2)
```

