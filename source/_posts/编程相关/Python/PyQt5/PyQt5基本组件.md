---
title: PyQt5基本组件
date: 2020-08-10 16:18:07
tags:
	- Python
	- GUI
categories:
	- 编程相关
	- Python
	- PyQt5
fileName: pyqt5-basic-widget
---



### 构建一般程序

#### 导入

```
import sys
from PyQt5.QtWidgets import QApplication, QWidget
```

#### 创建一个QApplication对象

每一个PyQt5程序都需要有一个QApplication对象。

```
app = QApplication(sys.argv)
```



#### 创建一个QWidget对象

不为控件指定一个父对象，那么该控件就会被当作窗口处理

```
window = QWidget()
window.show()
```

#### 进入主循环

进入程序主循环，事件处理从本行代码开始。主循环接收事件消息并将其分发给程序的各个控件。

使用`sys.exit()`方法退出可以确保程序完整的结束，若程序运行成功，`exec_()`的返回值为0，否则为非0。（与底层使用C++有关）

```
sys.exit(app.exec_())
```







## 布局管理

### 绝对布局





### 布局管理器

选中需要布局的几个元素，右键在“布局”中即可选择相应布局

使用布局管理器后，属性编辑器中geometry属性变为了灰色不可用，说明这些标签的位置大小已经由布局管理器接管了。





## 信号与槽

创建事件循环后，通过建立信号与槽的连接就可以实现对象之间的通信（信号发射时，连接的槽函数会自动执行）

QObject.signal.connect()



## 窗口

### QMainWindow

QMainWindow、QWidget、QDialog三个类都可以创建窗口。其中主窗口包含菜单栏等等，主窗口继承自（QWidget）





将按钮button的clicked信号与closeWin槽函数关联起来

```
self.button = QPushButton('关闭')
self.button.clicked.connect(self.closeWin)
```



### QWidget

基础窗口控件QWidget类是所有用户界面对象的基类，所有窗口和控件都直接或间接继承自QWidget



### QLabel





## 按钮

按钮的基类是QAbstractButton，提供了按钮的通用性功能



## 对话框

对话框的基类是QDialog，主要子类有QMessageBox、QFileDialog、QFontDialog、QInputDialog

### QMessageBox

弹出提示信息，各参数如下

```
QMessageBox.information(QWidget, title, text, buttons, defaultButton)
QMessageBox.warning()
QMessageBox.question()
QMessageBox.critical()
QMessageBox.about()
```

示例

```
QMessageBox.information(self, "标题", "关于对话框", QMessageBox.Yes | QMessageBox.No, QMessageBox.Yes)
```



### QInputDialog

该标准对话框可以收集控件输入的信息

```
self.btn2 = QPushButton("获得字符串")
self.btn2.clicked.connect(self.getIext)
self.le2 = QLineEdit()
```





```
def getIext(self):	
	text, ok = QInputDialog.getText(self, 'Text Input Dialog', '输入姓名:')
	if ok:
		self.le2.setText(str(text)) 
```



### QFileDialog

QFileDialog是用于打开和保存文件的标准对话框

主要方法如下

```
QFileDialog.getOpenFileName(父组件, 标题, 默认打开目录, 文件拓展名过滤器)
```

示例

```
f = QFileDialog.getOpenFileName()
print(f)
########################
('C:/Users/lenovo/Desktop/pyqt/t1.ui', 'All Files (*)')
```



## 绘图

主要可以通过QPainter、QPen与QBrush等三个类进行绘图

### QPainter

绘制方法在QPainter对象的begin()和end()之间。





## 多线程

GUI中部分程序十分耗时可能会造成卡顿，这时候就需要使用多线程技术。PyQt中主要涉及三个内容，一个是使用计时器模块QTimer；一种是使用多线程模块QThread，还有使用事件处理的功能

### QTimer



### QThread

PyQt线程使用非常简单，建立一个自定义的类（继承自QThread），并实现其run()方法





## 网页交互

PyQt5使用PyQt5.QtWebEngineWidgets中的QWebEngineView控件来展示HTML页面

### 方法





### 示例

```
class MainWindow(QMainWindow):

def __init__(self):
    super(QMainWindow, self).__init__()
    self.setWindowTitle('打开外部网页例子')
    self.setGeometry(5, 30, 1355, 730)
    self.browser = QWebEngineView()
    # 加载外部页面
    self.browser.load(QUrl('http://ziyedy.top'))
    self.setCentralWidget(self.browser)
```











