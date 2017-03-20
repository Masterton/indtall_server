# CentOs7 服务器环境安装

------

## 一、关闭selinux

> * sestatus -v 命令查看selinux的状态
> * 如果显示 selinux status = enable，则将selinux设置为disable
> * vim /etc/sysconfig/selinux 进入文件
> * 把 SELINUX=enabled 改为 SELINUX=disabled
> * 重启系统 shutdown -r now

------

## 二、设置为阿里云的源

> * 1、下载repo文件

  >> wget http://mirrors.aliyun.com/repo/Centos-7.repo
  
> * 2、备份并替换系统的repo文件

  >> cp Centos-7.repo /etc/yum.repo.d/
  >> cd /etc/yum.repos.d/
  >> mv CentOS-Base.repo CentOS-Base.repo.bak
  >> mv Centos-7.repo CentOS-Base.repo
  
> * 3、执行yum源更新命令 

  >> yum clean all
  >> yum makecache
  >> yum update
  
------

## 安装(nginx)

> * 1、gcc 安装

  >> 编译依赖 gcc 环境
  
  >> yum install gcc-c++
  
> * 2、PCRE pcre-devel 安装

  >> PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。

  >> yum install -y pcre pcre-devel
  
> * 3、zlib 安装

  >> zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip
  
  >> yum install -y zlib zlib-devel
  
> * 4、OpenSSL 安装

  >> OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。
  >> nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。

  >> yum install -y openssl openssl-devel
  
> * 5、安装nginx源

  >> rpm -ivh nginx-release-centos-7-0.el7.ngx.noarch.rpm
  
> * 6、安装nginx

  >> yum -y install nginx

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

```default.conf
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

  >> systemctl start nginx.servise
  >> systemctl restart nginx.servise
  >> systemctl enable nginx.servise

------

## 安装PHP（5.6n）和PHP-fpm

------

## 安装(MySQL)

------

## 安装(ssl或openssl)

------

## 设置防火墙（打开22、80、3306端口）

------
