---
title: latex基本用法
date: 2019-02-20 21:48:16
tags:
    - latex
    - 论文
categories:
    - 常用命令
    - latex
fileName: latex1
---
谈到码字与排版大家首先想到的一定是word等办公软件，即一些“所见即所得”的排版与“码字”软件，这些软件功能强大足够应付日常的一切工作。
但当大家有英文论文的写作与排版需求时，尤其是面对美赛这种“英文写作比赛”时，输出的PDF文件的美观性就尤其重要了（本人也是因为美赛才接触了latex这个让人又爱又恨的软件）。出于实用与给自己进行用法记录的目的，我也就不拿软件的历史之类的凑字数了，直接上可以产生效果的东西。
**PS：**我个人认为latex 与HTML之类的标签语言十分相似，学习成本低，易用难精（也没必要精），因此本文只针对相应排版的具体问题，不涉及任何latex的理论与底层问题，想精学的朋友建议移步刘海洋的《LaTeX入门》（可以联系本人电子版），同时有其他问题建议百度搜索或混混相应论坛，这儿给一个tackle problems的[传送门](http://www.latexstudio.net/),下载TEXLive请移步[传送](http://tug.org/texlive/),选择相应版本后耐心等待。
**以下未经说明使用TeXworks编辑器，编译方式选择XELaTeX**

## 目录及其相关设置
```
\tableofcontents            %插入目录，由于latex的“交叉引用”，注意应编译两次
\thispagestyle{empty}       %设置目录页不计入页码，用于文章其他部位亦可
\clearpage                  %该语句使得文章后面内容另起一页显示，此处使得目录单独写在一页
\setcounter{page}{1}        %设置目录后正文第一页目录计数为1
```

## 正文文章结构的设置
```
\section{一级标题内容}
正文
\subsection{二级标题内容}
正文
\subsubsection{三级标题内容}
正文
```
以上模式相信用过word的朋友都很容易理解了，同时通过上一条所述的目录内容会根据正文中的\section{}等标签自动生成文章目录，十分方便。

## 字体字号的常用设置
latex中包含了很多种字体，但如果没有特殊需求，我认为以下内容完全能够满足需要了
```
\textbf{内容}       %将大括号内字体加粗
\textit{内容}       %将大括号内字体变为斜体
\emph{内容}         %强调括号内字体内容
```

## 列表环境
### 有计数的列表
```
\begin{enumerate}
    \item 内容1
    \item 内容2
\end{enumerate}
```
### 无计数的列表（bulleted list）
```
\begin{itemize}
    \item 内容1
    \item 内容2
\end{itemize}
```
### 复杂列表
同时上述各个列表可以相互嵌套，如下即为在一个bulleted list下又嵌套了一个计数列表。
```
\begin{itemize}
    \item 内容1
    \begin{enumerate}
        \item 内容1.1
        \item 内容1.2
    \end{enumerate}
\end{itemize}
```

## 插入图片
在正文中插入图片很简单，如果你没有特殊的需求copy下面这段代码并且更改里面一些参数完全够用了，图片的资源文件和其他开发都是一样的，建议在根目录下建立一个img文件夹专门存放，同时记得将文件的命名与格式搞清楚。
```
%下行代码中[]内为可选项意义分别为
%h 代表here,将表格排在当前文字位置
%t 表示将表格放在下一页的 top (页首)
%b 表示将表格放在当前页的 bottom (底部)
%! 表示忽略美观因素，尽可能按照参数指定的方式来处理表格浮动位置
\begin{figure}[h]
    \centering      %使图片居中
    %下面这行可以简单理解为载入图片的函数，width=0.6\textwidth是将图片等比例设为宽度为0.6个间页长（排版的名词不大记得可以简单这么理解），你也可以使用px之类的将其设置为定宽。后面大括号内一定得是如image1.jpg这种格式，后缀不能漏
    \includegraphics[width=0.6\textwidth]{加后缀的图片名}
    \caption{图片的名称}    %大括号内为页面显示的图片的名称
    \label{fig:1}          %设置图片的标签方便交叉引用
\end{figure}
```

## 插入表格
表格是一切论文或其他议论文体中非常常见的数据表达形式，由于数据的不同各种类型多种多样，不想上节所述的图片那么一段代码打天下，所以表格内容较多，我尽量将我认为的常用的表格形式的通用代码附上，同时也列举一下表格的基本形式，方便读者进行变换。
### 普通表格
普通表格是指一个没有任何改动的最基本的表格（参考word你便知道样式），我也可以借由此说明一下表格中的一些常用参数
同时，如\centering；\caption{};\label{}等操作，与上一节所述关于图片相同
```
\begin{table}[!htb] %[]内与上一节所述图片相同
    \begin{tabular}{|c|r|l|}    %后一个括号内c代表居中，r代表靠右，l代表靠左，|为表格竖线，可以舍去
        \hline  %为表格横线，可以舍去
        内容1 & 内容2 & 内容3 \\    %每个单元格间由&分开，\\表示换行
        \hline
        内容4 & 内容5 & 内容6 \\
        \hline
    \end{tabular}
\end{table}
```
### 表格其他的常用设置
重新定义表格线的宽度代码如下，可随意更改使用相应代号的名称。使用时找到代码相应位置进行相应更改即可。
注意：**以下内容要放在导言区**
```
%定义表格横线\ttline的线宽为1.4pt，使用时用\ttline代替上一节中的\hline即可
\newcommand{\ttline}{
    \noalign {\ifnum 0=`}\fi \hrule height 1.4pt
    \futurelet \reserved@a \@xhline
}
%重定义表格竖线！的线宽为2pt，使用时用！代替上一节中的|即可
\newcolumntype{!}{@{\hskip\tabcolsep\vrule width 2pt\hskip\tabcolsep}}
\makeatother
```
剩下的一些小功能如下：
```
%设置表格标题置于表格上面（默认置于下面）
\setlength{\belowcaptionskip}{6pt}  %置于表格上面6pt

%如果想单独使一个单元格居中，可以这么写
\multicolumn{1}{c}{content}

%想调整表格上下间距，可以在\begin{table}前加（同样适用于更改正文行距）
\linespread{倍数} %更改后值为 倍数*默认间距

%合并单元格，合并行列可以使用以下公式进行嵌套
\multicolumn{项数}{新列格式}{单元格内容} %合并列
\multirow{项数}{新列格式}{单元格内容} %合并行

\cline{2-3} %表示画出一条位于第2列到第3列的横线段，其他列将不会有横线段
```
### 几种特殊表格代码
下面是几种相对特殊的表格的代码，根据这些代码结合前两小节的内容更改相应部分即可完成自己的制定。
```
\linespread{1.1}
\begin{table}[h]
\setlength{\belowcaptionskip}{6pt}\centering\caption{dia test}\label{tab:dia}
\begin{tabular}{ccc}\ttline
\diagbox{test1}{test2} & altitude & vavlue \\ \tline
testa & $22.2$ & $22.1$ \\ 
testb & $1.22$ & $2.22$ \\
\end{tabular}
\end{table}
```
此种表格应记住在导言区加上 **\usepackage{diagbox}**
![示例1](/assets/BlogPic/2.1/t1.png)
三线表是各种论文中较为常用的部分，代码与示例见下

## 插入公式及其他数学式
公式环境与图片和表格类似，也可以自动编号以及使用交叉引用，只需设置好\label{}内容即可，具体如下：
### 公式环境
一般公式编辑：
```
\begin{equation}
    公式内容
    \label{eq:1}
\end{equation}
```
分段函数模板：
```
\begin{equation}
p_{j}=\begin{cases} 
    0,j=2\\
    1,j=1
\end{cases}
\label{}
\end{equation}
```
### 常见数学公式的编辑