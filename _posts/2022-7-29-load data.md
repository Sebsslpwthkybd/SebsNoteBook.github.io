---
title: 数据操作
date: 2022-07-29 12:00:00 +0800
categories: [SogouTop50]
tags: [hadoop]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

### 将原始数据加载到HDFS

##### 启动Hadoop

```
start-all.sh
```

##### 创建存放目录

```
hdfs dfs -mkdir -p /data
```

##### 将数据集传入虚拟机（放入/data目录）

```
使用secureFX
```

##### 传输文件

```
hdfs dfs -put /data/sogou.500w.utf8 /data
```

##### 检查是否成功

```
hdfs dfs -ls /data
```

##### 删除上传文件

```
hdfs dfs -rm -r 路径
```

