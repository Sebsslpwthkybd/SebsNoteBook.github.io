---
title: linux下安装hadoop并部署全分布
date: 2022-06-01 16:00:00 +0800
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

## 安装单机hadoop

### 创建用户并配置无密码登录

创建hadoop用户

```
sudo useradd -m hadoop -s /bin/bash
```

改个密码

```
sudo passwd hadoop
```

赋予管理员权限

```
sudo adduser hadoop sudo
```

尝试使用密码登录localhost

```
ssh localhost
```

退出localhost

```
exit
```

配置无密码登录

```
cd ~/.ssh/
ssh-keygen -t rsa
cat ./id_rsa.pub >> ./authorized_keys
```

再次尝试进入localhost，不需要输入密码则成功

```
ssh localhost
```

### 安装java环境

创建文件夹

```
cd /usr/lib
sudo mkdir jvm
cd ~
sudo mkdir Donwloads  # 如果没有的话（gnomo桌面版有自带）
cd Downloads
```

传入jdk安装包

```
sudo rz -e
sudo sz dk-8u162-linux-x64.tar.gz
```

解压jdk

```
sudo tar -zxvf ./jdk-8u162-linux-x64.tar.gz -C /usr/lib/jvm
```

设置环境变量

```
cd ~
vim ~/.bashrc

# 开头加入
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_162
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

source ~/.bashrc
```

查看是否安装成功

```
java -version
```

### 安装Hadoop

传文件

```
cd Downloads

sudo rz -e
sudo sz hadoop-3.1.3.tar.gz
```

解压安装包

```
sudo tar -zxf ~/Downloads/hadoop-3.1.3.tar.gz -C /usr/local 
```

改名

```
cd /usr/local/
sudo mv ./hadoop-3.1.3/ ./hadoop 
```

修改文件权限

```
sudo chown -R hadoop ./hadoop
```

检查是否安装成功

```
cd /usr/local/hadoop
./bin/hadoop version
```

### 打包容器

查看容器id

```
docker ps
```

生成镜像

```
docker commit (container id) (image's name)
```

## Hadoop 伪分布式

修改配置

```
vim ./etc/hadoop/core-site.xml

<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/usr/local/hadoop/tmp</value>
        <description>Abase for other temporary directories.</description>
    </property>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```

```
vim ./etc/hadoop/hdfs-site.xml

<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/usr/local/hadoop/tmp/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/usr/local/hadoop/tmp/dfs/data</value>
    </property>
</configuration>
```

NameNode格式化

```
cd /usr/local/hadoop
./bin/hdfs namenode -format
```

启动hadoop服务

```
cd /usr/local/hadoop
./sbin/start-dfs.sh
```

关闭hadoop服务

```
cd /usr/local/hadoop
./sbin/stop-dfs.sh
```

## Hadoop 分布式

### master

创建新容器（master / slave）

```
docker run -p (port:port) --privileged -ti  --net staticnet --ip (static ip address) --name=(container name) (image's name) /bin/bash
```

开启ssh服务

```
service ssh restart
```

修改workers文件

```
cd /usr/local/hadoop/etc/hadoop
vim workers

删去localhost
加入Slave1
```

修改core-site.xml

```
vim core-site.xml

<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://Master:9000</value>
        </property>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>file:/usr/local/hadoop/tmp</value>
                <description>Abase for other temporary directories.</description>
        </property>
</configuration>
```

xxxxxxxxxx sudo apt install docker-ce docker-ce-cli containerd.iotext

```
vim hdfs-site.xml

<configuration>
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                <value>Master:50090</value>
        </property>
        <property>
                <name>dfs.replication</name>
                <value>1</value>  # 集群中的数据节点数量
        </property>
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>file:/usr/local/hadoop/tmp/dfs/name</value>
        </property>
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>file:/usr/local/hadoop/tmp/dfs/data</value>
        </property>
</configuration>
```

修改文件mapred-site.xml文件名

```
sudo mv ./mapred-site.xml.template ./mapred-site.xml
```

修改mapred-site.xml文件配置

```
vim mapred-site.xml

<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>Master:10020</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>Master:19888</value>
        </property>
        <property>
                <name>yarn.app.mapreduce.am.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property>
        <property>
                <name>mapreduce.map.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property>
        <property>
                <name>mapreduce.reduce.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property> 
</configuration>
```

修改yarn-site.xml

```
vim yarn-site.xml

<configuration>
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>Master</value>
        </property>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
</configuration>
```

清除伪分布下生成的临时文件

```
cd /usr/local
sudo rm -r ./hadoop/tmp
sudo rm -r ./hadoop/logs/*
tar -zcf ~/hadoop.master.tar.gz ./hadoop
cd ~
scp ./hadoop.master.tar.gz Slave1:/home/hadoop
```

