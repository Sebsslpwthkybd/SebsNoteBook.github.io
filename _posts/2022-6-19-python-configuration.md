---
title: 项目配置
date: 2022-06-19 15:00:00 +0800
categories: [线上商城]
tags: [configuration]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true




---

### 创建项目：

#### 使用后端django框架（virtualenv）

创建虚拟环境（进入该目录下）

```
virtualenv -p python3.8 django_py3.8
```

激活虚拟环境

```
django_py3.8/Scripts/activate
```

安装依赖

```
pip install django,mysqlclient,django_redis,Pillow,django-debug-toolbar
```

创建django项目

```
django-admin startproject traveling_django
```

配置django-debug-toolbar

```
settings.py

添加INSTALLED_APP:['debug-toolbar']
添加MIDDLEWARE = ['debug_toolbar.middleware.DebugToolbarMiddleware', ]
INTERNAL_IPS = ['127.0.0.1', ]

urls.py
if settings.DEBUG:
	urlpatterns = [path('__debug__/', include(debug_toolbar.urls))] + urlpatterns
```



#### 使用前端vue框架（nodejs）

安装vue脚手架

```
npm install -g @vue/cli
```

创建项目

```
vue create traveling_vue
```

进入项目

```
cd traveling_vue
```

安装依赖

```
npm install vue-router -S
```

```
npm install vant -S
```

```
npm install axios -S
```



#### 安装mysql

安装源(yum install)

https://dev.mysql.com/downloads/repo/yum/

查看可安装的mysql版本

```
yum repolist enabled | grep "mysql.*-community.*"
```

解锁mysql安装

```
yum repolist enabled | grep mysql
```

安装mysql

```
sudo yum install mysql-community-server
```

启动mysql

```
systemctl start mysqld
```

获取初始密码

```
sudo grep 'temporary password' /var/log/mysqld.log
```

使用初始密码登录

```
mysql -uroot -p
```

改密码（大小写特殊符号）

```
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'Paul34252780*';
```

设置允许远程访问

```
update mysql.user set host = '%' where user = 'root';
```

开放3306端口



#### 安装gcc编译器

```
yum install gcc gcc-c++
```



#### 安装redis

[Download | Redis](https://redis.io/download/)（选择5.0版本直接复制下载链接）

```
wget https://download.redis.io/releases/redis-5.0.14.tar.gz
```

解压源码

```
tar -zxvf redis-5.0.14.tar.gz
```

进入源码文件夹

```
cd redis-5.0.14
```

开始编译

```
make
```

运行安装

```
make install
```

修改配置文件使其可以在后台运行

```
vim redis.conf

修改为yes
daemonize yes
```

使用配置文件运行redis

```
./src/redis-server redis.conf
```



#### 安装python3.8.10

[Download Python | Python.org](https://www.python.org/downloads/)（选择3.8）

下载对应版本

```
wget https://www.python.org/ftp/python/3.8.13/Python-3.8.13.tgz
```

解压

```
tar -zxvf Python-3.8.13.tgz
```

检测能否正常安装(需要gcc编译器支持)

```
sh ./Python-3.8.13/configure
```

安装依赖

```
yum install zlib zlib-devel openssl openssl-devel
```

```
yum install glib2-devel openssl-devel pcre-devel bzip2-devel gzip-devel
```

```
yum install libffi-devel -y
```

```
yum install zlib zlib-devel
```

```
yum install libjpeg libjpeg-devel
```

```
yum install freetype freetype-devel
```

重新测试(千万不要加--opti什么的参数)

```
./Python-3.8.13/configure
```

编译

```
make
```

看到Python build finished successfully！则表示成功

运行安装

```
make install
```