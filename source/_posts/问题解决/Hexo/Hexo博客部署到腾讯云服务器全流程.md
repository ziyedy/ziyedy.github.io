---
title: Hexo博客部署到腾讯云服务器全流程
date: 2020-03-13 02:48:15
tags:
	- Hexo
	- 博客搭建
categories:
	- 问题解决
	- Hexo
fileName: hexo2
---

Hexo署到Githubpage上在国内访问速度很慢，正好前段时间买了一个腾讯云服务器，就干脆把hexo生成的博客文件直接放到云上跑算了，本文记录将hexo同时部署到GitHub与腾讯云服务器的过程。参考博客https://blog.csdn.net/StaunchKai/article/details/82878928

## 部署环境

* 本地环境
  * Ubuntu 18.04（64bit）
  * 所需环境：git，Node.js，hexo
* 服务器环境
  * 腾讯云（CentOS 7.6 64bit）
  * 所需环境：git，Nginx

## 服务器配置

### 安装git

安装依赖包：

```
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
yum install  gcc perl-ExtUtils-MakeMaker
```

卸载旧版本git（如果服务器自带git太旧的话）

```
cd /usr/local/src   // 选择文件保存位置
wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.19.0.tar.gz   // 下载链接
tar -zxvf git-2.19.0.tar.gz   // 解压
```

具体的版本选择可在https://mirrors.edge.kernel.org/pub/software/scm/git/ 查看。

编译安装

```
cd git-2.19.0   // 进入文件夹
make prefix=/usr/local/git all  // 编译源码
make prefix=/usr/local/git install  // 安装至 /usr/local/git 路径
```

之后正常的话`git --version`就可以看到git的版本了，如果在终端输入git没反应，则应该将git的安装位置加入环境变量中。

### 创建git用户

```
adduser git
passwd git
chmod 740 /etc/sudoers
vim /etc/sudoers
```

找到以下内容

```
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
```

在root的下面添加一行

```
git ALL=(ALL) ALL
```

保存退出后将权限改回来

```
chmod 400 /etc/sudoers
```

### 密匙配置

与将本地博客文件连接至GitHub一样，将本地的`id_rsa.pub`复制好，在服务器创建.ssh下的文件，粘贴进去。

```
su git
mkdir ~/.ssh
vim ~/.ssh/authorized_keys
```

修改权限

```
cd ~
chmod 600 .ssh/authorzied_keys
chmod 700 .ssh
```

### 测试连接

在本地终端输入

```
ssh -v git@SERVER
```

SERVER为你的服务器的公网地址，在控制台可以找到，如果成功连接说明服务器git配置成功。

### 创建网站根目录

网站根目录就是之后存放博客静态文件与资源的位置，你可以自己挑选位置创建你的根目录，之后再赋予权限。

```
su root
mkdir /home/myblog    # 此目录为网站的根目录
chown git:git -R /home/myblog
```

### 安装Nginx

```
yum install -y nginx    // 安装
systemctl start nginx.service     // 启动服务
```

此时访问**公网ip/80**应该就能看到nginx的初始界面。

### 配置Nginx

使用`nginx -t`查看配置文件位置，一般为`/etc/nginx/nginx.conf`

打开nginx.conf文件，修改配置文件如下

```
server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name  www.ziyedy.top;    # 修改为自己的域名
    root         /home/myblog;    # 修改为网站的根目录

    # Load configuration files for the default server block.
    include /etc/nginx/default.d/*.conf;

    location / {
    	index index.html	# 设置自己的index网页
    }

    error_page 404 /404.html;
        location = /40x.html {
    }

    error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    }
}

```

之后可以使用`nginx -t`检查配置文件中是否有语法错误，之后重启服务即可。

## hexo自动部署

### 建立git裸库

在与文件同一根目录下建立裸库

```
su root
git init --bare blog.git
```

这时，`git` 用户的 `~` 目录下就存在一个 `blog.git` 文件夹，可使用 `ls` 命令查看。再修改 `blog.git` 的权限。

```
chown git:git -R blog.git
```

### 使用git-hooks同步网站根目录

在这使用的是 `post-receive` 这个钩子，当 `git` 有收发的时候就会调用这个钩子。 在 `blog.git` 裸库的 hooks 文件夹中，新建 `post-receive` 文件。

```
vim blog.git/hooks/post-receive
```

填入以下内容，注意myblog应该填入自己的根目录文件名

```
#!/bin/sh
git --work-tree=/home/hexo --git-dir=/home/git/blog.git checkout -f
```

保存后，赋予该文件可执行权限

```
chmod +x /home/git/blog.git/hooks/post-receive
```

### 本地配置

与部署到GitHub相同，在本地博客文件根目录下的`_config.yml` 文件中修改`deploy`设置如下。SERVER为服务器的公网ip，如果不想部署到GitHub了将`github`一行删去即可。

```
deploy:
  type: git
  repo:
    github: git@github.com:xxxx/xxxx.github.io.git
    git1: git@SERVER:/home/git/blog.git 
  branch: master
```

之后正常配置

```
hexo clean
hexo g
hexo d
```

这是输入你的公网ip即可进入你的博客，要实现域名访问，在你的域名内解析你的公网ip即可。