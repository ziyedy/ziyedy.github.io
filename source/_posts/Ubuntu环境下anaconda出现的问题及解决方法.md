---
title: Ubuntu环境下anaconda出现的问题及解决方法
date: 2020-04-22 00:06:07
tags:
    - python
categories:
    - python
fileName: ubuntu_anaconda_problem
---

## 创建新环境报错
报错如下
```
Collecting package metadata (current_repodata.json): failed

NotWritableError: The current user does not have write permissions to a required path.
  path: /home/ziyedy/.conda/pkgs/urls.txt
  uid: 1000
  gid: 1000

If you feel that permissions on this path are set incorrectly, you can manually
change them by executing

  $ sudo chown 1000:1000 /home/ziyedy/.conda/pkgs/urls.txt

In general, it's not advisable to use 'sudo conda'.

```
按照提示尝试操作没用，解决方法是命令行输入如下：
```
sudo chown -R ziyedy anaconda3 # ziyedy is user name.
```
参考：https://blog.csdn.net/PecoHe/article/details/104578700

## 无法改变环境
正常情况下输入`conda activate <env_name>`时，python应该改变为该环境下的python版本，可以使用`python -V`查看，但有时命令行显示上环境已经更改但实际上所使用的python解释器没有更换，解决方法如下：
1. 使用记事本打开编辑环境（该目录下的.bashrc存储的为个人的一些配置文件，如别名路径）
```
sudo gedit ~/.bashrc
```
2. 在末尾按照规律添加如下内容
```
export PATH="export PATH="~/anaconda3/bin:$PATH"   #base环境下的python，已有的
alias python1="~/anaconda3/bin/python   # 你需要添加的
```
`alias`用于设置指令的别名，下面一条语句的意思就算你输入python1就会自动指向后面
3. 使得修改生效
```
source ~/.bashrc
```
