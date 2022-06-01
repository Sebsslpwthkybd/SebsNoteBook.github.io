---
title: linux基础配置
date: 2022-06-01 15:00:00 +0800
categories: [linux使用]
tags: [configuration]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true



---

拉取容器

```
docker run -p (port:port) --privileged -ti --name=(container name) ubuntu:(ubuntu version) /bin/bash
```

给root改密码

```
passwd root
```

更新下下载器

```
apt-get update
```

下载文件修改器

```
apt-get install vim
```

### 换源

复制一份备份

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

打开/etc/apt/sources.list文件，在前面添加如下条目，并保存。

```
sudo vim /etc/apt/sources.list
```

[ubuntu镜像-ubuntu下载地址-ubuntu安装教程-阿里巴巴开源镜像站 (aliyun.com)](https://developer.aliyun.com/mirror/ubuntu)



[ubuntu | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)

更新源

```text
sudo apt-get update
```

更新软件：

```text
sudo apt-get upgrade
```

### 基础环境的配置：

下个安全证书

```
sudo apt-get install --reinstall ca-certificates
```

ssh

```
apt-get install ssh
```

开启ssh服务

```
sudo /etc/init.d/ssh start
service ssh start/stop/restart
```

查看下ssh运行的状态

```
ps -e|grep ssh
service ssh status
```

![image-20220424162630580](D:\code\SebsNoteBook.github.io\_posts\2022-4-24-hadoop.assets\image-20220424162630580.png)

赋予ssh服务全部权限

```
chmod 777 /etc/ssh/ssh_config
```

修改下ssh配置文件

```
vim /etc/ssh/ssh_config

最后两行加入：
#  StrictHostKeyChecking no
#  UserKnownHostsFile /dev/null

vim /etc/ssh/sshd_config

把注释去掉：
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
PermitRootLogin yes
Port 22
ListenAddress 0.0.0.0
ListenAddress ::
```

将用户列表加入已知用户

```
vim ~/.ssh/config

写入：
UserKnownHostsFile ~/.ssh/known_hosts
```

尝试localhost

```
ssh localhost
```

添加net-tools工具包

```html
apt-get install net-tools
```

查看容器ip

```
ifconfig
```

### 配置ssh连接

右键虚拟网卡vEthernet(WSL)，找到Internet(TCP/IPv4)——属性

将ip地址改至与虚拟机同一网段下，子网掩码相同

打开SecureCRT，快速连接

Hostname:(虚拟网卡ip)

Port:22(或者是创建容器时22端口映射的端口：60001:22则写60001)

下载传输文件功能

```
sudo apt-get install lrzsz
```

传文件

```
cd (文件放入的目录)
sudo rz -e
```

接收文件

```
sudo sz (文件名)
```

