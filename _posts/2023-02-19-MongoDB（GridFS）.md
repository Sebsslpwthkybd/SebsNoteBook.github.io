---
title: 使用GridFS存储文件
date: 2023-02-19 15:00:00 +0800
categories: [NoSQL]
tags: [MongoDB]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

# 文件存储

例子：商品图片、用户头像

优点：读写速度快，文件存储引擎完善，适用于分布式环境，比普通sql数据库更适合存储文件

## GridFS存储引擎

主要用于存储超16M（BSON文件限制）的文件，对大文件有着更好的性能。

### 存储原理

用两个集合来存储文件

- database.chunks：存放文件
- database.files：存储文件的元数据

GridFS会把文件分割成若干chunks（256KB），然后在files记录它们

### 连接GridFS

```
from gridfs import GridFS
db = client.<database>  # 指定保存文件的逻辑库
gfs = GridFS(db, collection=<table>)  # 指定保存文件的集合
```

### 存储文件

put

```
# 打开文件
file = open("path", "rb")
args = {"type": <filetype>, "keyword": <keyword>}
gfs.put(file, filename=<filename>, **args)
file.close()
```

- 需要以二进制方式读取文件方可存入（rb）
- args为自己定义的描述信息（文件的元数据），例如文件类型、文件关键词等，以字典形式传入即可（方便日后通过描述信息提取该文件）
- filetype：文件类型
- keyword：关键词

### 查询文件

find_one

```
# 按照文件名查找
file = gfs.find_one({"filename": <filename>})
# 查看文件体积
print("%dM" % (math.ceil(file.length / 1024 / 1024)))
```

find

```
files = gfs.find(<conditions>})

# 查看文件更新日期
for one in files:
	uploadDate = one.uploadDate + datetime.timedelta(hours=8)
	uploadDate = uploadDate.strftime("%Y-%m-%d %H:%M:%S")
	print(one._id, one.filename, uploadDate)
```

- 查看文件体积

```
import math
# 将大小由b转m
size = "%dM" % (math.ceil(file.length / 1024 / 1024)))
```

- 查看文件更新日期

```
import datetime
# UTC转换北京时间
uploadDate = file.uploadDate + datetime.timedelta(hours=8)
# 格式化打印
uploadDate = uploadDate.strftime("%Y-%m-%d %H:%M:%S")
```

### 判断文件是否存在

exists

```
from bson.objectid import ObjectId
# 判断单个文件
gfs.exists(ObjectId(<objectid>))  # 必须封装成objectid才能查
# 判断查询结果是否为空
gfs.exists(**{key: value})
```

### 读取文件

get：只能通过主键查找文件

```
document = gfs.get(ObjectId(<objectid>))
file = open(<filepath>, "wb")
file.write(document.read())
file.close()
```

### 删除文件

delete：只能通过主键删除文件

```
gfs.delete(ObjectId(<objectid>))
```

