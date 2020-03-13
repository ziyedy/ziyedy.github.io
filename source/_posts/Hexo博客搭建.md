---
title: Hexo博客搭建
date: 2020-03-12 22:24:43
tags:
    - hexo
categories:
    - 记录
    - hexo
fileName: hexo1
---
hexo是一个基于Node.js的静态网页生成器，能直接将markdown文件生成为html文件，部署起来十分方便且有许多丰富的开源主题，是搭建博客的不二选择，具体可见[hexo官网](https://hexo.io/zh-cn/)，本文主要记录在Ubuntu 18.04下的Hexo博客的搭建过程。

### 准备环境
#### 安装git
```
sudo apt-get install git
```
可以直接使用命令行进行安装，但这种情况git版本往往较低，因此推荐前往[git官网](https://git-scm.com/)进行下载安装。安装完成后可以使用`git --version`查看一下版本。

#### 安装Node.js
进入[官网](http://nodejs.cn/download/)选择下载相关版本的Linux二进制文件（Hexo官网建议使用10.0及以上版本，而apt-get下载版本过老因此此处建议在官网下载最新版本），输入以下命令
```
tar xf  node-yourversion-linux-x64.tar.xz
cd node-yourversion-linux-x64/
./bin/node -v
```
如果显示了版本，说明解压安装成功，但此时在命令行输入node是没有反应的，因此还要输入以下命令设置软连接
```
ln -s /yourlocation/nodejs/bin/npm /usr/local/bin/ 
ln -s /yourlocation/nodejs/bin/node /usr/local/bin/ 
```
之后在命令行调用npm与node的相关命令，如果未显示“未找到命令”则说明安装成功。

#### 安装Hexo
```
npm install -g hexo-cli
```
在命令行输入以上命令即可安装hexo，但安装过后在命令行输入hexo显示未找到命令（hexo默认安装在与npm相同的位置），因此也要输入以下命令建立软链接。
```
ln -s /yourlocation/nodejs/bin/hexo /usr/local/bin/ 
```
之后在命令行测试无误，即安装成功。

#### 初始化博客本地目录
在你打算作为博客本地目录的文件夹下打开终端输入以下命令即可完成对hexo博客的初始化。
```
hexo init
npm install
```
此时文件夹下会出现如下目录：
```
.
├── _config.yml     //配置文件，用于配置网站基本信息
├── package.json    //安装相关插件的信息，一般可以忽略其内容
├── scaffolds   //模板文件夹，里面可以更改之后构建相关md文件的模板
├── source  //存放文章与各类资源的地方，被解析后相关内容会被放到生成的public文件夹内
|   ├── _drafts
|   └── _posts
└── themes  //存放主题的地方，可以自己选择或开发主题放进来
```
此时在终端输入
```
hexo s
```
如果不出bug，在http://localhost:4000/ 便能访问到博客的初始界面了。

#### Windows下的安装方法
Windows下的安装方法更简单，到相关依赖项的官网上下载一直确定就能够安装成功。之后在git bash下的操作就与Linux基本一致了。

### 网站部署
#### GithubPage
1. 创建github账户并在本地创建ssh密匙，之后在github网站中的**setting**中找到SSH相关选项，将本地的ssh密匙添加进去即可（本地的ssh密匙都是在`.ssh\id_rsa.pub`中可以找到）。

2. 在github下新建仓库，仓库命名必须为`<账户名称>.github.io`
3. 修改本地博客文件中_config.yml中的设置如下
```
deploy:
  type: git
  repo: git@github.com:<Github账号名称>/<Github账号名称>.github.io.git
  branch: master
```
4. 在博客本地目录下使用如下命令即可完成网页的生成与部署。之后在https://<Github账号名称>.github.io即可访问到自己的页面了。
```
hexo g  //生成
hexo d  //部署
```

#### 云服务器

