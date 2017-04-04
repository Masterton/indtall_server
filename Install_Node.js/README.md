# node.js
@(简单演示)[node.js|npm]

------

##node.js安装

### 1、[Node.js安装包及源码下载](https://nodejs.org/en/download/)

> 你可以根据不同平台系统选择你需要的Node.js安装包。

### 2、windows下安装

> 1、**下载windows版的**

> ![](http://www.runoob.com/wp-content/uploads/2014/03/download-page.jpg)

> 2、每一步直接 **next** 就行
> 3、安装完成运行 node --version 查看版本

> ![](http://www.runoob.com/wp-content/uploads/2014/03/node-version-test.png)

### 3、Ubuntu 上安装 Node.js

> 1、在 Github 上获取 Node.js 源码：

```
$ sudo git clone https://github.com/nodejs/node.git
Cloning into 'node'...
```

> 2、修改目录权限：

```
$ sudo chmod -R 755 node
```

> 3、使用 ./configure 创建编译文件，并按照：

```
$ cd node
$ sudo ./configure
$ sudo make
$ sudo make install
```

> 4、查看 node 版本：

```
$ node --version
v0.10.25
```

> 5、Ubuntu apt-get命令安装

```
sudo apt-get install nodejs
sudo apt-get install npm
```

### 4、CentOS 下安装 Node.js

> 1、下载源码，你需要在https://nodejs.org/en/download/下载最新的Nodejs版本，本文以v0.10.24为例:

```
cd /usr/local/src/
wget http://nodejs.org/dist/v0.10.24/node-v0.10.24.tar.gz
```

> 2、解压源码

```
tar zxvf node-v0.10.24.tar.gz
```

> 3、 编译安装

```
cd node-v0.10.24
./configure --prefix=/usr/local/node/0.10.24
make
make install
```

> 4、配置NODE_HOME，进入profile编辑环境变量

```
vim /etc/profile
#设置nodejs环境变量，在 export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL 一行的上面添加如下内容:
#set for nodejs
export NODE_HOME=/usr/local/node/0.10.24
export PATH=$NODE_HOME/bin:$PATH
#:wq保存并退出，编译/etc/profile 使配置生效
source /etc/profile
#验证是否安装配置成功
node -v
#输出 v0.10.24 表示配置成功
#npm模块安装路径
/usr/local/node/0.10.24/lib/node_modules/
```

### 5、简单使用 node.js

> 1、新建 server.js 代码如下：


```javascript
var http = require('http');

http.createServer(function (request, response) {

	// 发送 HTTP 头部 
	// HTTP 状态值: 200 : OK
	// 内容类型: text/plain
	response.writeHead(200, {'Content-Type': 'text/plain'});

	// 发送响应数据 "Hello World"
	response.end('Hello World\n');
}).listen(8888);

// 终端打印如下信息
console.log('Server running at http://127.0.0.1:8888/');
```

> 2、使用 node 命令执行以上的代码：

```
node server.js
Server running at http://127.0.0.1:8888/
```

![](http://www.runoob.com/wp-content/uploads/2014/03/cmdrun.jpg)

> 3、接下来，打开浏览器访问 http://127.0.0.1:8888/，你会看到一个写着 "Hello World"的网页。

![](http://www.runoob.com/wp-content/uploads/2014/03/nodejs-helloworld.jpg)

> 4、具体使用请查看 [Node.js 教程](http://www.runoob.com/nodejs/nodejs-tutorial.html)