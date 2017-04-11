# CentOs7 服务器环境安装

------

## 一、关闭selinux

> * sestatus -v 命令查看selinux的状态
> * 如果显示 selinux status = enable，则将selinux设置为disable
> * vim /etc/sysconfig/selinux 进入文件
> * 把 SELINUX=enabled 改为 SELINUX=disabled

```
# 重启系统 
shutdown -r now 
# 关机
poweroff
```

------

## 二、设置为阿里云的源

> * 1、下载repo文件

```
wget http://mirrors.aliyun.com/repo/Centos-7.repo
```
  
> * 2、备份并替换系统的repo文件

```
cp Centos-7.repo /etc/yum.repo.d/
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo.bak
mv Centos-7.repo CentOS-Base.repo
```
  
> * 3、执行yum源更新命令 

```
yum clean all
yum makecache
yum update
```
  
------

## 安装(nginx)

> * 1、gcc 安装

  >> 编译依赖 gcc 环境

```
yum install gcc-c++
```
  
> * 2、PCRE pcre-devel 安装

  >> PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。

```
yum install -y pcre pcre-devel
```
  
> * 3、zlib 安装

  >> zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip

```
yum install -y zlib zlib-devel
```
  
> * 4、OpenSSL 安装

  >> OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。
  >> nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。

```
yum install -y openssl openssl-devel
```
  
> * 5、安装nginx源

```
rpm -ivh nginx-release-centos-7-0.el7.ngx.noarch.rpm
```
  
> * 6、安装nginx

```
yum -y install nginx
```

> * 7、配置nginx

  >> /etc/nginx/nginx.conf 配置文件代码（如下）
  
```nginx.conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;
    
    keepalive_timeout  65;

    gzip  on;

    include /etc/nginx/conf.d/*.conf;
}

```
  
  >> /etc/nginx/conf.d/default.conf 配置文件代码（如下）

```
server {
    listen       80;
    server_name  localhost;

    charset utf8;
    #access_log  /var/log/nginx/log/host.access.log  main;

    location / {
        root   /home/zheng/www/laravel/;
        index  index.php index.html index.htm;
        try_files $uri $uri/ /index.php?$args;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        root           html;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        fastcgi_param  SCRIPT_NAME      $fastcgi_script_name;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    location ~ /\.ht {
        deny  all;
    }
}


```
  
> * 9、启动、重启、设置开机启动

```
systemctl start nginx.servise ##启动服务
systemctl restart nginx.servise ##重启服务
systemctl enable nginx.servise ##添加到开机启动服务中去
```

------

## 安装PHP（5.6n）和PHP-fpm

> * 1、检查当前安装的PHP包

```
yum list installed | grep php (查看当前的PHP安装包)
yum remove (删除指定的包)
php -m 查看安装的PHP扩展
```
  
> * 2、添加Remi源

  >> http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
  
  >> Remi源默认是没有启用的，我们来启用Remi源，修改 /etc/yum.repos.d/remi.repo 文件，把文件内的 enabled=0 改为 enabled=1 ，注意：改文件内有2个 enabled=0 我们修改[remi]下面的，不要修改[remi-test]下面的。
  
> * 3、运行 yum install 安装PHP和PHP扩展

```
yum install php56w.x86_64
yum install php56w-fpm
yum install php56w-cli.x86_64
yum install php56w-common.x86_64
yum install php56w-gd.x86_64
yum install php56w-ldap.x86_64
yum install php56w-mbstring.x86_64
yum install php56w-mcrypt.x86_64
yum install php56w-mysql.x86_64
yum install php56w-pdo.x86_64
```
  
> * 4、安装xmlwriter、xmlreader

  >> 1、查看libxml、libxml2包安装没

```
php -i | grep "xml"
```
  
```
/etc/php.d/20-simplexml.ini,
/etc/php.d/20-xml.ini,
/etc/php.d/20-xmlwriter.ini,
/etc/php.d/30-xmlreader.ini,
xmlrpc_error_number => 0 => 0
xmlrpc_errors => Off => Off
PHP Warning:  Unknown: It is not safe to rely on the system's timezone settings. You are *required* to use the date.timezone setting or the date_default_timezone_set() function. In case you used any of those methods and you are still getting this warning, you most likely misspelled the timezone identifier. We selected the timezone 'UTC' for now, but please set date.timezone to select your timezone. in Unknown on line 0
libxml Version => 2.9.1
libxml
mbstring.http_output_conv_mimetypes => ^(text/|application/xhtml\+xml) => ^(text/|application/xhtml\+xml)
Simplexml support => enabled
xml
libxml2 Version => 2.9.1
xmlreader
xmlwriter
libxslt compiled against libxml Version => 2.9.1

```

  >> 如果没安装就安装 yum install libxml2

```
yum install php-xmlwriter
yum install php-xmlreader
yum install php-xml
```
  
> * 5、设置开机启动、启动、重启

```
systemctl start php-fpm.servise
systemctl restart php-fpm.servise
systemctl enable php-fpm.servise
```

------

## 安装(MySQL)

------

> 1、在MySQL官网中下载YUM源rpm安装包：
  >> http://dev.mysql.com/downloads/repo/yum/ 

![](http://www.centoscn.com/uploads/allimg/160626/1-160626010PSQ.jpg)

```
// 下载 MySQL 源安装包
wget http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
// 安装 MySQL 
yum localinstall mysql57-community-release-el7-9.noarch.rpm
// 检查 MySQL 源是否安装成功
yum repolist enabled | grep "mysql.*-community.*"
```

> 2、安装MySQL

```
[root@iZ28gvqe4biZ ~]# yum -y install mysql-community-server
已加载插件：langpacks
正在解决依赖关系
--> 正在检查事务
---> 软件包 mysql-community-server.x86_64.0.5.6.28-2.el7 将被 安装
--> 正在处理依赖关系 mysql-community-common(x86-64) = 5.6.28-2.el7，它被软件包 mysql-community-server-5.6.28-2.el7.x86_64 需要
--> 正在处理依赖关系 mysql-community-client(x86-64) = 5.6.28-2.el7，它被软件包 mysql-community-server-5.6.28-2.el7.x86_64 需要
--> 正在处理依赖关系 perl(DBI)，它被软件包 mysql-community-server-5.6.28-2.el7.x86_64 需要
--> 正在检查事务
---> 软件包 mysql-community-client.x86_64.0.5.6.28-2.el7 将被 安装
--> 正在处理依赖关系 mysql-community-libs(x86-64) = 5.6.28-2.el7，它被软件包 mysql-community-client-5.6.28-2.el7.x86_64 需要
---> 软件包 mysql-community-common.x86_64.0.5.6.28-2.el7 将被 安装
---> 软件包 perl-DBI.x86_64.0.1.627-4.el7 将被 安装
--> 正在处理依赖关系 perl(RPC::PlServer) >= 0.2001，它被软件包 perl-DBI-1.627-4.el7.x86_64 需要
--> 正在处理依赖关系 perl(RPC::PlClient) >= 0.2000，它被软件包 perl-DBI-1.627-4.el7.x86_64 需要
--> 正在检查事务
---> 软件包 mariadb-libs.x86_64.1.5.5.40-1.el7_0 将被 取代
---> 软件包 mysql-community-libs.x86_64.0.5.6.28-2.el7 将被 舍弃
---> 软件包 perl-PlRPC.noarch.0.0.2020-14.el7 将被 安装
--> 正在处理依赖关系 perl(Net::Daemon) >= 0.13，它被软件包 perl-PlRPC-0.2020-14.el7.noarch 需要
--> 正在处理依赖关系 perl(Net::Daemon::Test)，它被软件包 perl-PlRPC-0.2020-14.el7.noarch 需要
--> 正在处理依赖关系 perl(Net::Daemon::Log)，它被软件包 perl-PlRPC-0.2020-14.el7.noarch 需要
--> 正在处理依赖关系 perl(Compress::Zlib)，它被软件包 perl-PlRPC-0.2020-14.el7.noarch 需要
--> 正在检查事务
---> 软件包 perl-IO-Compress.noarch.0.2.061-2.el7 将被 安装
--> 正在处理依赖关系 perl(Compress::Raw::Zlib) >= 2.061，它被软件包 perl-IO-Compress-2.061-2.el7.noarch 需要
--> 正在处理依赖关系 perl(Compress::Raw::Bzip2) >= 2.061，它被软件包 perl-IO-Compress-2.061-2.el7.noarch 需要
---> 软件包 perl-Net-Daemon.noarch.0.0.48-5.el7 将被 安装
--> 正在检查事务
---> 软件包 perl-Compress-Raw-Bzip2.x86_64.0.2.061-3.el7 将被 安装
---> 软件包 perl-Compress-Raw-Zlib.x86_64.1.2.061-4.el7 将被 安装
--> 解决依赖关系完成

依赖关系解决

==============================================================================================
 Package                       架构         版本                源                       大小
==============================================================================================
正在安装:
 mysql-community-libs          x86_64       5.6.28-2.el7        mysql56-community       2.0 M
      替换  mariadb-libs.x86_64 1:5.5.40-1.el7_0
 mysql-community-server        x86_64       5.6.28-2.el7        mysql56-community        58 M
为依赖而安装:
 mysql-community-client        x86_64       5.6.28-2.el7        mysql56-community        19 M
 mysql-community-common        x86_64       5.6.28-2.el7        mysql56-community       256 k
 perl-Compress-Raw-Bzip2       x86_64       2.061-3.el7         base                     32 k
 perl-Compress-Raw-Zlib        x86_64       1:2.061-4.el7       base                     57 k
 perl-DBI                      x86_64       1.627-4.el7         base                    802 k
 perl-IO-Compress              noarch       2.061-2.el7         base                    260 k
 perl-Net-Daemon               noarch       0.48-5.el7          base                     51 k
 perl-PlRPC                    noarch       0.2020-14.el7       base                     36 k

事务概要
==============================================================================================
安装  2 软件包 (+8 依赖软件包)

总下载量：80 M
Downloading packages:
警告：/var/cache/yum/x86_64/7/mysql56-community/packages/mysql-community-common-5.6.28-2.el7.x86_64.rpm: 头V3 DSA/SHA1 Signature, 密钥 ID 5072e1f5: NOKEY
mysql-community-common-5.6.28-2.el7.x86_64.rpm 的公钥尚未安装
(1/10): mysql-community-common-5.6.28-2.el7.x86_64.rpm                 | 256 kB  00:00:14     
(2/10): mysql-community-libs-5.6.28-2.el7.x86_64.rpm                   | 2.0 MB  00:02:57     
(3/10): perl-Compress-Raw-Zlib-2.061-4.el7.x86_64.rpm                  |  57 kB  00:00:00     
(4/10): perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64.rpm                 |  32 kB  00:00:00     
(5/10): perl-IO-Compress-2.061-2.el7.noarch.rpm                        | 260 kB  00:00:00     
(6/10): perl-Net-Daemon-0.48-5.el7.noarch.rpm                          |  51 kB  00:00:00     
(7/10): perl-PlRPC-0.2020-14.el7.noarch.rpm  
(8/10): perl-DBI-1.627-4.el7.x86_64.rpm                                | 802 kB  00:00:00     
(9/10): mysql-community-server-5.6.28-2.el7.x86_64.rpm                 |  58 MB  00:54:45     
(10/10): mysql-community-client-5.6.28-2.el7.x86_64.rpm                |  19 MB  00:20:42     
总计                                                           13 kB/s |  80 MB  01:45:50     
从 file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql 检索密钥
导入 GPG key 0x5072E1F5:
 用户ID     : "MySQL Release Engineering <mysql-build@oss.oracle.com>"
 指纹       : a4a9 4068 76fc bd3c 4567 70c8 8c71 8d3b 5072 e1f5
 软件包     : mysql-community-release-el7-5.noarch (installed)
 来自       : file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
警告：RPM 数据库已被非 yum 程序修改。
  正在安装    : mysql-community-common-5.6.28-2.el7.x86_64                               1/11 
  正在安装    : mysql-community-libs-5.6.28-2.el7.x86_64                                 2/11 
  正在安装    : mysql-community-client-5.6.28-2.el7.x86_64                               3/11 
  正在安装    : 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                              4/11 
  正在安装    : perl-Net-Daemon-0.48-5.el7.noarch                                        5/11 
  正在安装    : perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                               6/11 
  正在安装    : perl-IO-Compress-2.061-2.el7.noarch                                      7/11 
  正在安装    : perl-PlRPC-0.2020-14.el7.noarch                                          8/11 
  正在安装    : perl-DBI-1.627-4.el7.x86_64                                              9/11 
  正在安装    : mysql-community-server-5.6.28-2.el7.x86_64                              10/11 
  正在删除    : 1:mariadb-libs-5.5.40-1.el7_0.x86_64                                    11/11 
  验证中      : perl-Compress-Raw-Bzip2-2.061-3.el7.x86_64                               1/11 
  验证中      : perl-Net-Daemon-0.48-5.el7.noarch                                        2/11 
  验证中      : mysql-community-common-5.6.28-2.el7.x86_64                               3/11 
  验证中      : perl-IO-Compress-2.061-2.el7.noarch                                      4/11 
  验证中      : mysql-community-libs-5.6.28-2.el7.x86_64                                 5/11 
  验证中      : mysql-community-client-5.6.28-2.el7.x86_64                               6/11 
  验证中      : 1:perl-Compress-Raw-Zlib-2.061-4.el7.x86_64                              7/11 
  验证中      : perl-DBI-1.627-4.el7.x86_64                                              8/11 
  验证中      : mysql-community-server-5.6.28-2.el7.x86_64                               9/11 
  验证中      : perl-PlRPC-0.2020-14.el7.noarch                                         10/11 
  验证中      : 1:mariadb-libs-5.5.40-1.el7_0.x86_64                                    11/11 

已安装:
  mysql-community-libs.x86_64 0:5.6.28-2.el7   mysql-community-server.x86_64 0:5.6.28-2.el7  

作为依赖被安装:
  mysql-community-client.x86_64 0:5.6.28-2.el7  mysql-community-common.x86_64 0:5.6.28-2.el7 
  perl-Compress-Raw-Bzip2.x86_64 0:2.061-3.el7  perl-Compress-Raw-Zlib.x86_64 1:2.061-4.el7  
  perl-DBI.x86_64 0:1.627-4.el7                 perl-IO-Compress.noarch 0:2.061-2.el7        
  perl-Net-Daemon.noarch 0:0.48-5.el7           perl-PlRPC.noarch 0:0.2020-14.el7            

替代:
  mariadb-libs.x86_64 1:5.5.40-1.el7_0                                                        

完毕！
```

> 3、启动MySQL服务

```
systemctl enable mysqld #加入开机启动
systemctl start mysqld #启动MySQL服务
systemctl restart mysqld #重启MySQL服务
```

> 4、重置密码

```
[root@iZ28gvqe4biZ ~]# mysql_secure_installation



NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MySQL to secure it, we'll need the current
password for the root user.  If you've just installed MySQL, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MySQL
root user without the proper authorisation.

Set root password? [Y/n] y        [设置root用户密码]
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MySQL installation has an anonymous user, allowing anyone
to log into MySQL without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y            [删除匿名用户]
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n            [禁止root远程登录]
 ... skipping.

By default, MySQL comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y       [删除test数据库]
 - Dropping test database...
ERROR 1008 (HY000) at line 1: Can't drop database 'test'; database doesn't exist
 ... Failed!  Not critical, keep moving...
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y        [刷新权限]
 ... Success!




All done!  If you've completed all of the above steps, your MySQL
installation should now be secure.

Thanks for using MySQL!


Cleaning up...
```

```
//登录MYSQL（有ROOT权限）。这里我以ROOT身份登录
[root@iZ28gvqe4biZ ~]# mysql -u root -p
//首先为用户创建一个数据库hivemeta
mysql  > create database hivemeta;
mysql  > use hivemeta
//授权hdp用户拥有hivemeta数据库的所有权限。
mysql  > grant all privileges on *.* to hdp@"%" identified by "hdp" with grant option;
//刷新系统权限表
mysql  > flush privileges;
mysql  > use hivemeta;
//mysql/hive字符集问题
mysql  > alter database hivemeta character set latin1;
```

## 安装(ssl或openssl)

------

## 设置防火墙（打开22、80、3306端口）

------

> 1.CentOS7 下默认使用的防火墙是 **firewall**

```
firewall-cmd --state //查看运行状态
//开放8080端口
firewall-cmd --add-port=8080/tcp permanent
// 重载生效刚才的端口设置
firewall-cmd --reload
// 另一种
firewall-cmd --permanent --zone=public --add-port=22/tcp
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --permanent --zone=public --add-port=3306/tcp
```

> 2.firewall 常用命令如下：

```
##常用命令介绍
firewall-cmd --state ##查看防火墙状态，是否是running
firewall-cmd --reload ##重新载入配置，比如添加规则之后，需要执行此命令
firewall-cmd --get-zones ##列出支持的zone
firewall-cmd --get-services ##列出支持的服务，在列表中的服务是放行的
firewall-cmd --query-service ftp ##查看ftp服务示范支持，返回yes或者no
firewall-cmd --add-service=ftp ##临时开放ftp服务
firewall-cmd --add-service=ftp --permanent ##永久开放ftp服务
firewall-cmd --remove-service=ftp --permanent ##永久一处ftp服务
firewall-cmd --add-port=80/tcp --permanten ##永久添加80端口
iptables -L -n ##查看规则，这个命令是和iptables的相同的
man firewall-cmd ##查看帮助
firewall-cmd --list-all-zones ##查看所有的zone信息
firewall-cmd --add-service=http ##暂时开放http
firewall-cmd --permanent --add-service=http ##永久开放http
firewall-cmd --zone=public --add-port=80/tcp --permanent ##在public中永久开放80端口
firewall-cmd --list-all ##查看防火墙开启了那些服务和端口