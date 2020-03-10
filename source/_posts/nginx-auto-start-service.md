---
title: Nginx开机自启动服务
copyright: true
related_posts: true
tags:
  - Nginx
  - Linux
categories: 技术
abbrlink: 4a76bf21
date: 2020-03-09 20:16:08
---


在我们在Linux搭建程序服务的过程中有时会有开关机的需要，所以不少服务（比如：Nginx、数据库等）都需要重新开启，有什么好的方式来让机器自动启动代替人工呢？
本文章将介绍一种比较通用且简单的方式：在 `/etc/init.d` 里配置启动脚本实现开机自启动。
<!--more-->

## /etc/init.d

在linux系统中大家一定听过init.d目录，这个目录究竟是做什么用的？

1. `/etc/init.d` 目录中存放的是一系列系统服务的管理（启动与停止）脚本。
2. 用service命令可执行该目录中相应服务的脚本。
3. /etc/init.d是指向/etc/rc.d/init.d的软连接

## 启动脚本

编写 **shell** 脚本来实现 nginx 服务的启停控制

``` bash /etc/init.d/nginx
#!/bin/bash
# nginx Startup script for the Nginx HTTP Server
# it is v.0.0.2 version.
# chkconfig: - 85 15
# description: Nginx is a high-performance web and proxy server.
#              It has a lot of features, but it's not for everyone.
# processname: nginx
# pidfile: /var/run/nginx.pid
# config: /usr/local/nginx/conf/nginx.conf
nginxd=/usr/local/nginx/sbin/nginx
nginx_config=/usr/local/nginx/conf/nginx.conf
nginx_pid=/var/run/nginx.pid
RETVAL=0
prog="nginx"
# Source function library.
. /etc/rc.d/init.d/functions
# Source networking configuration.
. /etc/sysconfig/network
# Check that networking is up.
[ ${NETWORKING} = "no" ] && exit 0
[ -x $nginxd ] || exit 0
# Start nginx daemons functions.
start() {
if [ -e $nginx_pid ];then
   echo "nginx already running...."
   exit 1
fi
   echo -n $"Starting $prog: "
   daemon $nginxd -c ${nginx_config}
   RETVAL=$?
   echo
   [ $RETVAL = 0 ] && touch /var/lock/subsys/nginx
   return $RETVAL
}
# Stop nginx daemons functions.
stop() {
        echo -n $"Stopping $prog: "
        killproc $nginxd
        RETVAL=$?
        echo
        [ $RETVAL = 0 ] && rm -f /var/lock/subsys/nginx /var/run/nginx.pid
}
# reload nginx service functions.
reload() {
    echo -n $"Reloading $prog: "
    #kill -HUP `cat ${nginx_pid}`
    killproc $nginxd -HUP
    RETVAL=$?
    echo
}
# See how we were called.
case "$1" in
start)
        start
        ;;
stop)
        stop
        ;;
reload)
        reload
        ;;
restart)
        stop
        start
        ;;
status)
        status $prog
        RETVAL=$?
        ;;
*)
        echo $"Usage: $prog {start|stop|restart|reload|status|help}"
        exit 1
esac

```

## 配置自启动

> **chkconfig** 命令主要用来更新(启动或停止)和查询系统服务的运行级信息。

每个被chkconfig管理的服务需要在对应的init.d下的脚本加上两行注释。
+ 第一行告诉chkconfig缺省启动的运行级以及启动和停止的优先级。如果某服务缺省不在任何运行级启动，那么使用 - 代替运行级；
+ 第二行对服务进行描述，可以用\ 跨行注释。

例如，nginx 脚本里包含的这两行：

``` bash
# chkconfig: - 85 15
# description: Nginx is a high-performance web and proxy server.
```
编写完脚本后，设置可执行权限并将其添加到 **chkconfig** 命令中管理。

+ 设置权限
  
  ``` bash
  chmod 775 nginx
  ```

+ 添加服务
  
  ``` bash
  chkconfig --add nginx
  ```

+ 设置开机运行

  ``` bash
  chkconfig nginx on 
  ```

+ 查看管理的服务
  
  ``` bash
  chkconfig --list nginx
  ```

至此，开启自启动的服务就配置完成了，不妨重启服务器试试效果吧。

## 拓展

对于其他服务也同样适用，比如 **Mysql**, **Redis**, **Mongodb** 等等，不过相应的配置脚本也有变化 ，大家可以自行 Googel。