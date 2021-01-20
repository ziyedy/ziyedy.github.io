---
title: Git出现的报错合集
date: 2020-06-15 23:31:46
tags:
	- git
categories:
	- 问题解决
fileName: git_error
---



### Git: fatal: refusing to merge unrelated histories解决

今天`git pull`时出现这个错误（在连接远程库与本地库时在远程库上进行了一些删除操作）。原因是两个分支为两个不同的版本，有不同的提交历史，使用以下语句进行强制合并即可解决这个问题。

```
$git pull origin master --allow-unrelated-histories
```



### fatal: remote origin already exists

先将存在的库删除，再进行连接

```
git remote rm origin
```



### non-fast-forward

没有对代码进行及时的同步产生的问题，应该先合并之前的历史，再进行提交

```
git fetch origin master
git merge origin FETCH_HEAD 
```

