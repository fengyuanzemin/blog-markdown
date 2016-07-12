---
title: 阿里云ECS-Ubuntu配置
date: 2016-06-29 14:00
tags: Ubuntu
---

首先吧，我们肯定已经有一个阿里云的ECS服务器啦

# 登录

```sh
ssh user@host
```
如果之前设置过ssh的话，就直接登录进去了。

没有的话就输入登录密码就可以了。

# 更新
先更新一下

```sh
apt-get update
	
apt-get upgrade
```

# 安装Git

先输入

```sh
git
```
查看git有没有安装，然后它如果提示你没安装的话，就输入下面的命令

```sh
apt-get install git
```

# 安装mysql

```sh
apt-get install mysql-server
```

# 安装nginx

安装nginx前面必须安装pcre、zlib和ssl 
## 安装编译平台

```sh
apt-get install build-essential
apt-get install libtool
```

## 安装OpenSSL

```sh
wget ftp://ftp.openssl.org/source/openssl-1.1.0-pre5.tar.gz
tar zxvf openssl-1.1.0-pre5.tar.gz
cd openssl-1.1.0-pre5
./config --prefix=/usr/local --openssldir=/usr/local/ssl
make && make install

./config shared --prefix=/usr/local --openssldir=/usr/local/ssl
make clean
make && make install
```

## 安装zlib
```sh
wget http://zlib.net/zlib-1.2.8.tar.gz
tar zxvf zlib-1.2.8.tar.gz
cd zlib-1.2.8
./configure --prefix=/usr/local
make && make install 
```

## 安装pcre
```sh
wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.37.tar.gz
tar zxvf pcre-8.37.tar.gz
cd pcre-8.37
./configure --prefix=/usr/local
make && make install
```

## 安装nginx

```sh
wget http://nginx.org/download/nginx-1.11.1.tar.gz
tar zxvf nginx-1.11.1.tar.gz
cd nginx-1.11.1

./configure --sbin-path=/usr/local/nginx/nginx \
--conf-path=/usr/local/nginx/nginx.conf \
--pid-path=/usr/local/nginx/nginx.pid \
--with-http_ssl_module \
--with-pcre=/usr/local/src/pcre-8.37 \
--with-zlib=/usr/local/src/zlib-1.2.8 \
--with-openssl=/usr/local/src/openssl-1.1.0-pre5

make && make install
```

安装成功后 /usr/local/nginx 目录下如下

```
fastcgi.conf            koi-win             nginx.conf.default
fastcgi.conf.default    logs                scgi_params
fastcgi_params          mime.types          scgi_params.default
fastcgi_params.default  mime.types.default  uwsgi_params
html                    nginx               uwsgi_params.default
koi-utf                 nginx.conf          win-utf
```

开机启动 Nginx

```sh
cd /etc/init.d/
vi nginx
```

添加以下内容：

```sh
#! /bin/bash
#
# nginx   Start up the nginx server daemon
#
# chkconfig: 2345 55 25
# Description: starts and stops the nginx web server
#
### BEGIN INIT INFO
# Provides:       nginx
# Required-Start:      $all
# Required-Stop:      $all
# Default-Start:        2 3 4 5
# Default-Stop:        0 1 6
# Description:         starts and stops the nginx web server
### END INIT INFO

# To install:
#   copy this file to /etc/init.d/nginx
#   shell> chkconfig –add nginx (RedHat)
#   shell> update-rc.d -f nginx defaults (debian)

# To uninstall:
#   shell> chkconfig –del nginx (RedHat)
#   shell> update-rc.d -f nginx remove

PATH=/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin
NAME=nginx
DAEMON=/usr/local/nginx/$NAME
CONFIGFILE=/usr/local/nginx/$NAME.conf
PIDFILE=/var/run/$NAME.pid
ULIMIT=10240

set -e
[ -x “$DAEMON” ] || exit 0

do_start() {
   echo “Starting $NAME …”
   ulimit -SHn $ULIMIT
   $DAEMON -c $CONFIGFILE
}

do_stop() {
   echo “Shutting down $NAME …”
   kill `cat $PIDFILE`
}

do_reload() {
   echo “Reloading $NAME …”
   kill -HUP `cat $PIDFILE`
}

case “$1” in
   start)
     [ ! -f “$PIDFILE” ] && do_start || echo “nginx already running”
  echo -e “.\ndone”
     ;;
   stop)
    [ -f “$PIDFILE” ] && do_stop || echo “nginx not running”
  echo -e “.\ndone”
     ;;
  restart)
  [ -f “$PIDFILE” ] && do_stop || echo “nginx not running”
     do_start
  echo -e “.\ndone”
     ;;
  reload)
   [ -f “$PIDFILE” ] && do_reload || echo “nginx not running”
  echo -e “.\ndone”
     ;;
    *)
    N=/etc/init.d/$NAME
    echo “Usage: $N {start|stop|restart|reload}” >&2
    exit 1
    ;;
esac

exit 0
```

添加执行权限

```sh
chmod a+x nginx 
```

添加启动项
	
```sh
update-rc.d -f nginx defaults
```

卸载启动项(仅卸载使用)

```sh
update-rc.d -f nginx remove
```


有时想知道nigix是否在正常运行，命令查看nginx运行情况。

执行命令：

```sh
ps -A | grep nginx
```

如果出现以下错误

	[emerg]: bind() to 0.0.0.0:80 failed (98: Address already in use)

则是端口被占用，此时要先关闭占用端口的软件，然后再启动nginx

```sh
fuser -k 80/tcp
service nginx start
```



# Ubuntu locales (设置语言)

```sh
locale-gen zh_CN zh_CN.UTF-8
dpkg-reconfigure locales
```

# 安装Nodejs

```sh
apt-get install curl
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
apt-get install -y nodejs
```