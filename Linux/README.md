# Linux 操作系统（常用命令）

------

> 开启外部访问端口的权限

```
iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
```

> 查看端口使用情况

```
netstat -anp|grep 80 (查看指定端口)
lsof -i (端口号查看某个端口是否被占用)
netstat –apn (查看所有的进程和端口使用情况)
killall php (结束所有事php的进程)
kill -9 500 (强制结束pid为500的进程)