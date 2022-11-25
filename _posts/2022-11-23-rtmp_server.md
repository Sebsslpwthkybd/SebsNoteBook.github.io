---
title: 搭建rtmp推流服务器
date: 2022-11-23 10:00:00 +0800
categories: [linux]
tags: [fire-smoke-detect]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

# ubuntu：22.04

下载依赖

```
sudo apt-get install libssl-dev
 
sudo apt-get install libpcre3 libpcre3-dev
 
sudo apt-get install openssl libssl-dev 
 
sudo apt-get install zlib1g-dev  
 
sudo apt-get install git
 
sudo apt-get install wget

sudo apt install build-essential
```

下载编译包

```
cd ~/rtmp
```

```
wget https://nginx.org/download/nginx-1.22.1.tar.gz
```

```
sudo tar -xvf nginx-1.22.1.tar.gz
```

```
sudo mv nginx-1.22.1 nginx
```

```
cd /usr/local/src
```

```
sudo git clone https://gitcode.net/mirrors/arut/nginx-rtmp-module.git
```

编译

```
cd ~/rtmp/nginx
```

```
sudo ./configure --add-module=/usr/local/src/nginx-rtmp-module
```

```
make install
```

开启

```
cd /usr/local/nginx/sbin
```

```
sudo ./nginx
```

检查开启

```
ps -ef|grep nginx
```

访问nginx服务器

```
ifconfig
```

```
http://{server ip}/

页面提示：
Welcome to nginx!
If you see this page, the nginx web server is successfully installed and working. Further configuration is required.

For online documentation and support please refer to nginx.org.
Commercial support is available at nginx.com.

Thank you for using nginx.
```

关闭

```
pkill nginx
```
