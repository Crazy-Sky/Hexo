---
title: Hexo博客部署到纯净CentOS 7云服务器上
date: 2021-07-03 11:22:00
tag: Hexo
categories: 网站建设
toc: true
thumbnail: https://pic2.zhimg.com/v2-f9654b817205f6af3e472af284ecc2b2_1200x500.jpg
---

Hexo博客部署腾讯云的CVM云服务器上，纯净的CentOS 7.5 64位版本，以Nginx作为Web服务器，网站实现自动发布

<!--more-->

## 博客自动发布架构

**前提条件**：

* 本地Hexo博客环境搭建完毕
* 云服务器上已部署好Nginx
* 云服务器上已部署好Git，并初始化一个用于存储Hexo的Git裸库
* 配置git-hooks，自动将Hexo仓库checkout到Nginx配置的网站根目录

**发布流程**：

本地编辑完博客之后，通过`hexo clean`、`hexo g -d`将本地生成的博客静态资源提交至云服务器上的Git裸库，git-hooks自动将更新的Git库内容同步至Nginx的网站目录，用户通过访问Nginx配置的网站地址即可访问博客网站。

![博客自动发布架构](Hexo博客部署到纯净CentOS%207云服务器上/1.webp)

## 本地环境配置

可参考B站up主**CodeSheep**的[手把手教你从0开始搭建自己的个人博客 |无坑版视频教程| hexo](https://www.bilibili.com/video/BV1Yb411a7ty)

## 云服务器环境配置

### 基础环境配置

```shell
# 安装编译环境
yum -y install gcc gcc-c++

# 安装vim编辑器
yum -y install vim*

# 安装pcre软件包（使nginx支持http rewrite模块，用于支持解析正则表达式）
yum install -y pcre pcre-devel

# 安装openssl-devel（使nginx支持ssl，用于网站加密通讯）
yum install -y openssl openssl-devel

# 安装zlib（用于对数据进行解压缩。网站之间通信时，数据先压缩再传输，通过消耗CPU的方式来节省网络带宽。）
yum install -y zlib zlib-devel
```

### Nginx配置

安装Nginx

```sh
# 下载Nginx
wget http://nginx.org/download/nginx-1.16.0.tar.gz

# 解压到/usr/local/
tar xzf nginx-1.16.0.tar.gz -C /usr/local/

# 进入Nginx
cd /usr/local/nginx-1.16.0/

# 执行配置文件configure
./configure

# 编译并安装
make && make install

# 开放linux的80端口
/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT

# 启动Nginx
cd /usr/local/nginx/sbin
./nginx

# 测试Nginx启动是否成功（或浏览器输入IP+端口，进入nginx的欢迎页面）
wget http://127.0.0.1
```

为Hexo博客网站配置Nginx服务器路由

```shell
# 创建网站目录
mkdir /home/hexo

# 修改权限
chown git:git -R /home/hexo

# 编辑nginx的配置文件nginx.conf
vim /usr/local/nginx/conf/nginx.conf

# localhost修改为公网IP
```

![Nginx配置](Hexo博客部署到纯净CentOS%207云服务器上/2.png)

```sh
# 修改/home/hexo权限
chown -R 755 /home/hexo

# 重新启动Nginx（停止./nginx -s stop）
cd /usr/local/nginx/sbin 
./nginx -s reload
```

### Git配置

安装Git

```shell
yum install -y git
```

配置Git的环境变量

```shell
# 编辑文件
vim /etc/profile

# 添加环境变量
PATH=$PATH:/usr/local/git/bin
export PATH

# 保存退出后，刷新环境变量
source /etc/profile
```

添加Git用户

```shell
# 创建git用户
adduser git

# 修改git用户权限
chmod 740 /etc/sudoers

# 编辑sudoers文件
vim /etc/sudoers

# 在“root    ALL=(ALL)       ALL”下添加
git     ALL=(ALL)       ALL

# 保存退出后，修改回原来的权限
chmod 400 /etc/sudoers

# 设置git用户的密码
sudo passwd git
```

给git用户配置ssh免密公钥登录

```shell
# 将当前用户从root用户切换到git用户
su git

# 创建.ssh文件夹
mkdir ~/.ssh

# 在.ssh文件夹下创建秘钥文件
vim ~/.ssh/authorized_keys

# 将本地的 id_rsa.pub 文件全部复制进去，退出保存后，修改文件权限（可百度ssh密钥）
chmod 600 ~/.ssh/authorized_keys
chmod 700 ~/.ssh

# 测试配置是否成功，如ssh git@127.0.0.1
ssh git@host_name
```

创建git仓库

```shell
# 将当前用户从git用户切换到root用户
su root

# 创建hexo.git仓库
git init --bare /home/git/hexo.git 

# 修改权限
chown git:git -R /home/git/hexo.git
```

### 配置git-hooks

```shell
# 在该仓库中创建钩子文件夹post-receive
vim /home/git/hexo.git/hooks/post-receive 

# 填入
git --work-tree=/home/hexo --git-dir=/home/git/hexo.git checkout -f

# 保存退出后，修改权限让其可执行
chmod +x /home/git/hexo.git/hooks/post-receive
```

### 本地环境配置

修改hexo本地站点配置文件_config.yml，hostname改为服务器IP

```yml
deploy:
  type: git
  repo:
      git@hostname:/home/git/hexo.git
  branch: master
```

## 博客发布

本地hexo博客目录下执行

```shell
# 清理静态文件
hexo clean

# 生成静态文件
hexo g

# 发布
hexo d
```

---

**学习所得，资料、图片部分来源于网络，如有侵权，请联系本人删除。**

**才疏学浅，若有错误或不当之处，可批评指正，还请见谅！**