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

```
apt-cache search mysql
apt-get install mysql-server
apt-get purge mysql-server

apt-get update
apt-get upgrade
apt-get install -f
```

```
# dpkg 命令用法 （Debian package）
dpkg -l package 显示包版本
dpkg -r package 移除软件（保留配置）
dpkg -P package 移除软件（不保留配置）
dpkg -s package 查找包的详细信息
dpkg -c package.deb 列出 deb 包的内容
dpkg -l | grep php 查看安装的包
```

```
apt-cache search # ------(package 搜索包)
apt-cache show #------(package 获取包的相关信息，如说明、大小、版本等)
apt-get install # ------(package 安装包)
apt-get install # -----(package --reinstall 重新安装包)
apt-get -f install # -----(强制安装, "-f = --fix-missing"当是修复安装吧...)
apt-get remove #-----(package 删除包)
apt-get remove --purge # ------(package 删除包，包括删除配置文件等)
apt-get autoremove --purge # ----(package 删除包及其依赖的软件包+配置文件等（只对6.10有效，强烈推荐）)
apt-get update #------更新源
apt-get upgrade #------更新已安装的包
apt-get dist-upgrade # ---------升级系统
apt-get dselect-upgrade #------使用 dselect 升级
apt-cache depends #-------(package 了解使用依赖)
apt-cache rdepends # ------(package 了解某个具体的依赖,当是查看该包被哪些包依赖吧...)
apt-get build-dep # ------(package 安装相关的编译环境)
apt-get source #------(package 下载该包的源代码)
apt-get clean && apt-get autoclean # --------清理下载文件的存档 && 只清理过时的包
apt-get check #-------检查是否有损坏的依赖
dpkg -S filename -----查找filename属于哪个软件包
apt-file search filename -----查找filename属于哪个软件包
apt-file list packagename -----列出软件包的内容
apt-file update --更新apt-file的数据库

dpkg --info "软件包名" --列出软件包解包后的包名称.
dpkg -l --列出当前系统中所有的包.可以和参数less一起使用在分屏查看. (类似于rpm -qa)
dpkg -l |grep -i "软件包名" --查看系统中与"软件包名"相关联的包.
dpkg -s 查询已安装的包的详细信息.
dpkg -L 查询系统中已安装的软件包所安装的位置. (类似于rpm -ql)
dpkg -S 查询系统中某个文件属于哪个软件包. (类似于rpm -qf)
dpkg -I 查询deb包的详细信息,在一个软件包下载到本地之后看看用不用安装(看一下呗).
dpkg -i 手动安装软件包(这个命令并不能解决软件包之前的依赖性问题),如果在安装某一个软件包的时候遇到了软件依赖的问题,可以用apt-get -f install在解决信赖性这个问题.
dpkg -r 卸载软件包.不是完全的卸载,它的配置文件还存在.
dpkg -P 全部卸载(但是还是不能解决软件包的依赖性的问题)
dpkg -reconfigure 重新配置
```
