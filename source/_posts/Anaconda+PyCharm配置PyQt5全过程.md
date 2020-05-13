---
title: Anaconda+PyCharm配置PyQt5全过程
date: 2020-04-09 22:44:55
tags:
	- python
	- GUI
categories:
	- Python
	- PyQt5
fileName: pyqt1
---



前段时间写了一个手写电话号码识别的小程序，GUI用的是Python自带的Tkinter，但这种GUI太简陋可视化效果不佳，因此打算使用别的更加强大的GUI重构一下，目光就瞄准了pyqt，因为用C++时接触过，知道这是个好用的GUI吧。

## Anaconda下安装

在Anaconda在GitHub上的issue下知道由于兼容Python2的原因没法用conda安装最新版PyQt5包，所以得使用pip命令进行安装。

在Anaconda自带的prompt下输入以下命令即可完成安装。

```
pip3 install PyQt5
```

但速度通常很慢，因此建议改为清华的镜像进行安装。

```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple PyQt5
```

之后在命令行窗口输入`import PyQt5`，不报错，说明安装成功。

## Pycharm下配置相关环境

用过Qt的应该都知道Qt有QtDesigner这种优秀的可视化设计工具，可以简化编码，所以如果使用PyCharm作为IDE编写Python的话需要将QtDesigner作为拓展程序进行配置（这样可以在IDE中直接进行调用了，会很方便）。除此之外，还需要配置将QtDesigner生成的.ui文件转化为.py文件。

### 配置QtDesigner

在PyCharm中的`File->Settings`下，进行如下操作，即可配置完成QDesigner。

![](..\_img\Anaconda+PyCharm配置PyQt5全过程\1.png)

### 配置PyUIC

使用QDesigner生成的文件格式为.ui的格式，要将其转化为.py格式安装的pyqt包中也已经写好的相应的程序。在上图中的相同界面中输入以下信息。

![](..\_img\Anaconda+PyCharm配置PyQt5全过程\2.png)

program中输入pyuic5.exe的路径。

在参数栏中输入如下命令：(该命令就是将.ui转化为.py文件)

```
$FileName$ -o $FileNameWithoutExtension$.py
```

该步骤中也有直接输入pyuic.py文件的，但我试验了以下没有成功，所以使用.exe文件还是最靠谱的（Windows下）。

## 使用实验

在菜单栏“工具”中找到拓展工具，选择QDesigner即可。

![](..\_img\Anaconda+PyCharm配置PyQt5全过程\3.png)



打开设计工具之后，即可创建相应的ui界面如下：

![](..\_img\Anaconda+PyCharm配置PyQt5全过程\4.png)

这时候可以看到PyCharm界面中多了一个.ui文件，在该文件上右击，选择拓展工具中的PyUIC即可完成.ui到.py的转换。

![](C:\Users\lenovo\Desktop\myblog\ziyedy.github.io\source\_img\Anaconda+PyCharm配置PyQt5全过程\5.png)

此时可以发现多了一个同名的.py文件，打开文件里面包含的就是之前设计好的界面的python代码了，加入以下主函数运行：

```
import sys
from PyQt5.QtWidgets import QApplication, QMainWindow
if __name__ == '__main__':
    app = QApplication(sys.argv)
    MainWindow = QMainWindow()
    ui = Ui_MainWindow()
    ui.setupUi(MainWindow)
    MainWindow.show()
    sys.exit(app.exec_())
```

即可得到如下结果：

![](..\_img\Anaconda+PyCharm配置PyQt5全过程\6.png)

OK，那使用PyQt5构建python的GUI程序的流程总算整通了。

#### 参考

https://www.jianshu.com/p/094928ac0b73

