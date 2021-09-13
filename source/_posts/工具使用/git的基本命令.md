---
title: git的基本命令
date: 2019-03-04 21:26:47
tags:
    - git
categories:
    - 工具使用
fileName: git-basic
---
## Git 小记
Git是使用最广泛的版本控制系统，加上GitHub如此闻名（假笑），所以学习Git的一些基本命令还是很重要的，本人是学习了廖雪峰老师的Git相关教程，并将其中Git的一些相关命令记录下来，方便查找，也算一个笔记（本人原来从不记笔记，现在改过自新了）。嘻嘻。
廖雪峰老师的Git学习[传送门](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)
首先应该明确的我认为有这么两个东西，即*工作区（Working Directory）*与*版本库（Repository）*。工作区可以简单理解成存放你工作内容的文件夹（本地），版本库则可以简单理解为记录你修改等操作内容的git生成的一个文件。版本库中又主要包含*暂存区*以及*分支结构master（默认）*。通过git提交项目修改之类的过程，可以简单理解为在本地进行修改后通过**git add**与**git commit**两个命令步骤，由本地到暂存区再到分支结构，这个命令的具体使用，后续会说明，这儿只对原理做一些粗略阐述。
同时分支结构有啥用呢？我觉得不必细谈，反正就理解为n个人可以一起工作还不会乱的一种机制吧，有Git需求的人想必能够理解，在此不做赘述（三言两语我也讲不清楚嘻嘻）

# Git 常用命令

选中文件夹不用一个个cd输入路径，选中相应文件夹鼠标右键选择*Git Bash Here*就行（Windows下）。
**PS：以下说明中<>括号内表示是你要输入的信息**

## git仓库处理

### 克隆远程代码

将远程代码克隆到本地

```
git clone <远程代码链接>
```

### 初始化git仓库
即将你选中的目录变为git管理的仓库，会自动生成一个`.git`文件夹
```
$ git init
```

### 查看基础信息

查看仓库当前的状态（文件增删，文件内容改变等）
```
$ git status
```
查看相应文件修改的具体内容
```
$ git diff <file>
```
显示从最近到最远的提交日志（包括上一节中你输入的说明，时间等内容），较为详细。在其之后加上相应备注后（行二），可以简略输出（id号码等等）
```
$ git log
$ git log --pretty=oneline
```
显示你的每一次命令（可以用来查看之前的每一步操作）
```
$ git reflog
```









### 提交本地修改

提交单个文件或文件夹，记得加后缀。同时file后用空格分割可以一次add很多文件

```
$ git add <file>
```

把暂存区的内容提交到当前分支（Git小记中的第二过程哦），`<message>`表示的是你对这此提交做的说明，类似于备忘录，方便你以后查看，同时每次使用*commit*命令后系统会生成一个id来记录这次提交，亦能方便查找

```
$ git commit -m <message>
```







### 版本回退

Git中有一个HEAD指针指向当前版本，而HEAD^表示上个版本，HEAD^^表示上上个版本，HEAD~n表示第n个版本。
回退到之前的版本于回退到相应版本的命令如下，其中<commit id>相信你已经明白了，这个就是上一节信息当中的版本号。
```
$ git reset --hard HEAD^
$ git reset --hard <commit id>
```
撤销工作区的修改，请注意这里的关键词*工作区*，也就是说这个操作只能将你还没有执行**add**命令的文件修改去除。
```
$ git checkout -- <file>
```
要去除已经使用add进入暂存区的修改，请使用以下命令命令。第一行是将暂存区的修改撤销掉（unstage），第二行就接着使用上面的命令就好，不重复写了。
```
$ git reset HEAD <file>
```
以上便是应对“工作区修改”、“暂存区修改”、“已提交”这三种情况的版本回退命令，足够应付大部分时间了。

### 删除文件
第一行命令：删除本地文件。
第二行命令：从版本库中删除文件(删除后记得再使用commit命令提交一次)
至于删错了恢复，请见上一节。
```
$ rm <file>
$ git <file>
```



## 分支（branch）操作

### 创建/切换分支（checkout）

分支在第一大节也有提到，这里列出一些常用操作
第一行为创建名称为`<name>`的分支，并将当前分支切换为`<name>`。第二行为切换当前分支为`<name>`。（同时，master为默认主分支）

```
$ git checkout -b <name>
$ git checkout <name>
```
### 查看分支（branch）

查看本地分支

```
$ git branch
```
查看远程分支。【-r 即 remote】

```
$ git branch -r
```

查看所有分支【-a 即 all】

```
git branch -a
```

### 创建（克隆）远程分支

该命令很重要，可以避免手动创建本地分支与远程分支无法对应的问题，避免分支创建出错导致后续开发出错。【-t 表示 trace 跟踪】

```
git checkout  -t origin/xxx
```



### 合并分支（merge）

将分支`<name>`合并到当前分支中

```
git merge <name>
```
### 保证本地与远程master分支同步（常用）

1.首先切换到主分支

```
git checkout master
```

2.使用git pull 把领先的主分支代码pull下来

```
git pull
```

3.切换到自己的分支

```
git checkout xxx(自己的分支)
```

4.把主分支的代码merge到自己的分支

```
git merge master
```

5.将分支代码推上远程库，即完成了与master分支代码的同步

```
git push origin 自己分支名
```



### 删除本地分支

删除`<name>`分支

```
git branch -d <name>
```

强行删除未进行合并操作的`<name>`分支

```
git branch -D <name>
```

### 删除远程分支

删除远程分支XXXX

```
git push origin --delete XXXX
```





查看分区合并历史以及合并图

```
$ git log --graph --pretty=oneline --abbrev-commit
```
**PS：**同时应注意，当你在master分支与新建的分支上都进行了修改并执行了commit命令时，直接合并可能会把报错，然后你得去相关文件中手动修改一下再commit。git会在相应文件中给你进行两次修改的醒目标注，很方便

## 标签（tag）操作

大家都知道软件会有版本，比如adobe公司的一堆CS和CC，tag在Git中就起到给一个commit设定一个除去id外更容易记住的别名的作用，方便查找。默认设置tag是设置在最近一次commit上。
以下第一行为设置一个标签；第二行为在id为commit_id的commit上设置标签；第三行在第二行的基础上给tag一点说明；第四行为查看现有所有标签；第五行查看相应标签详细信息；第六行删除一个本地标签。
```
$ git tag <tagname>
$ git tag <tagname> <commit_id>
$ git tag -a <tagname> -m <message> <commit_id>
$ git tag
$ git show <tagname>
git tag -d <tagname>
```

### 远程操作
前面提到了GitHub，自然学git远程操作（或者理解成跟GitHub玩游戏也成）是必不可少的，本地下载了git咋和GitHub扯上关系就略过不谈了，以下记录一些相关常用操作。
刚下好git打开就会有以下这些玩意，你也可以随时使用相应命令查看相应配置，使用**git config -1**可以查看很多信息，试试就知道。
```
$ git config --global user.email "your_email@qq.com"
$ git config --global user.name "your_github_name"
```
将本地库与远程库相关联（origin是远程库的默认名称）
```
$ git remote add origin git@<server-name:path/repo-name.git>
```
使用*push*命令，将本地库的内容推送到远程库上(第一次)，之后每次本地修改后若要推送可用第二行命令
```
$ git push -u origin master
$ git push origin master
```
将GitHub上的内容克隆到本地（注意你要是想对其有修改并推送，应先fork到自己的库里，再克隆）。输入git clone后将你所要克隆的网址复制后加上就好。
```
$ git clone <http:~>
```
查看远程库详细相关信息
```
$ git remote -v
```
建立远程库dev分支与本地dev的链接
```
$ git branch --set-upstream-to=origin/dev dev
```
把最新的提交抓取下来
```
$ git pull
```

### 有关参考
其实很多命令压根不用记，但要去看每个操作下来git给的提示，很多后续如何操作已经写好了，你所需的往往只是做一做英语阅读。



git关联远程分支：

https://blog.csdn.net/weijinqian0/article/details/76217014



## git 暂存的使用

### 使用场景

当需要切换分支又不想将当前分支的修改提交时，可以使用暂存操作

比如：阅读源代码并且做了注释（熟悉代码时可以新建一个read-code分支用于阅读），此时需要切换到其他分支时可以使用暂存区。或者需要更新主分支也可以先将本地分支暂存后切换

### 常用操作流程

1、暂存操作

```
#查看当前状态
git status 
#如果有修改,添加修改文件
git add .
#暂存操作
git stash save '本次暂存的标识名字'
```

2、查看当前暂存记录

```
#查看记录
git stash list
```

3、恢复暂存的工作

恢复指定的暂存工作, 暂存记录保存在list内,需要通过list索引index取出恢复

pop命令恢复,恢复后,暂存区域会删除当前的记录

```
git stash pop stash@{index}
```

apply命令恢复,恢复后,暂存区域会保留当前的记录

```
git stash apply stash@{index}
```

4、删除暂存

```
#删除某个暂存, 暂存记录保存在list内,需要通过list索引index删除
git stash drop stash@{index}
#删除全部暂存
git stash clear
```

