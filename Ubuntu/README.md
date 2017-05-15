# Ubuntu16.04 服务器环境安装

------

### 一、安装nginx（或者apache）


```
apache目录结构
├── apache2.conf #apache住配置文件
├── conf-available
├── conf-enabled #允许的配置项
├── envvars
├── magic
├── mods-available
├── mods-enabled #允许apache加载的模块
├── ports.conf #apache开启的端口文件
├── sites-available
└── sites-enabled #允许访问的配置
```

------

### 二、安装php7.0

```
# ubuntu lamp环境搭建
apt-get install mysql-server \
    mysql-client \
    libssl-dev \
    gcc \
    g++ \
    php7.0 \
    php-dev \
    apache2 \
    libapache2-mod-php7.0 \
    php7.0-fpm \
    php-pear \
    php7.0-common \
    php7.0-gd \
    php7.0-zip \
    php7.0-mysql \
    php7.0-mbstring \
    php7.0-xml \
    php7.0-mcrypt \
    git \
    openssh-client \
    openssh-server

# 启用rewrite模块
a2enmod rewrite
a2enmod expires

# apache 基本命令
service apache2 start	#开启apache
service apache2 stop	#停止apache
service apache2 restart #重启apache

```

------

### 三、安装MySQL

------

### 四、git命令

```
git cloen 仓库地址 本地的文件夹 	#克隆仓库
git diff 文件路径	#对比文件内容
git branch 分支名	#创建分支
git commit	#提交到本地仓库
git push	#提交到远程仓库
git merge 分支	#合并分支
git fetch 分支	#更新
git pull 分支	#拉取文件到本地
```

------

### 五、Ubuntu（apt操作命令）

```
netstat -apn 查看端口占用详情
service mysql start 启动服务
service mysql stop 停止服务
service mysq restart 重启服务
service mysql status 查看运行状态
ps -aux | grep nginx 查看nginx进程
```

------

### apt-get 命令详解

```
apt-get remove php5 卸载php5
dpkg --get-selections|grep php 查看安装的php扩展apt-cache search package 搜索软件包
apt-cache show package  获取包的相关信息，如说明、大小、版本等
sudo apt-get install package 安装包
sudo apt-get install package --reinstall   重新安装包
sudo apt-get -f install   修复安装
sudo apt-get remove package 删除包
sudo apt-get remove package --purge 删除包，包括配置文件等
sudo apt-get update  更新源
sudo apt-get upgrade 更新已安装的包
sudo apt-get dist-upgrade 升级系统
apt-cache depends package 了解使用该包依赖那些包
apt-cache rdepends package 查看该包被哪些包依赖
sudo apt-get build-dep package 安装相关的编译环境
apt-get source package  下载该包的源代码
sudo apt-get clean && sudo apt-get autoclean 清理无用的包
sudo apt-get check 检查是否有损坏的依赖
```

apt-cache search mysql
apt-get install mysql-server
apt-get purge mysql-server

apt-get update
apt-get upgrade
apt-get install -f
