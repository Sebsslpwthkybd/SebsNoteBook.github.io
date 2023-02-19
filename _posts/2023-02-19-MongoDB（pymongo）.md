---
title: 使用pymongo进行交互
date: 2023-02-19 10:00:00 +0800
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

# 安装

## 拉包

```
pip install pymongo==3.7.2
```

## API接口清单

[Tutorial — PyMongo 4.3.3 documentation](https://pymongo.readthedocs.io/en/stable/tutorial.html)

# 使用

## 创建连接（MongoClient）

MongoClient是客户端代理对象，可用于执行增删改查、连接池

```
from pymongo import MongoClient
client = MongoClient(host=<host>, port=<port>)  # 创建链接
client.admin.authenticate(<user>, <password>)  # 验证用户
```

- Error:'Collection' object is not callable 是由于pymongo版本高于4.0引起的，解决办法：

  ```
  import pymongo
  client = pymongo.MongoClient(
  							host=<host>,
  							port=27017,
  							username=<user>,
  							password=<password>)
  ```

## 写入

insert_one

```
client.<dbname>.<tablename>.insert_one({<key: value>})
```

insert_many

```
client.<dbname>.<tablename>.insert_many([
{<key: value>},
{<key: value>}, 
...
])
```

## 查询

find_one

```
cursor = client.<dbname>.<tablename>.find_one(<conditions>)

# 获取查询结果
print(cursor["_id"], cursor[<column>], ...)
```

find

```
cursor = client.<dbname>.<tablename>.find(<conditions>)

# 获取查询结果
for one in cursor:
	print(one["_id"], one[<column>], ...)
```

## 修改

update_one

```
# 修改元素
client.<dbname>.<tablename>.update_one(
<conditions>, {"$set": <key: value>})

# 添加元素
client.<dbname>.<tablename>.update_one(
<conditions>, {"$push": <key: value>})
```

update_many

```
# 如果没有该元素就增加该元素，如果有该元素，则该元素elements+1
client.<dbname>.<tablename>.update_many(
<conditions>, {"$set": {"key": ["value"]}})
```

## 删除

delete_one

```
# 如果有多条记录，只会删除第一条
client.<dbname>.<tablename>.delete_one(<conditions>)
```

delete_many

```
# 删除符合条件的记录
client.<dbname>.<tablename>.delete_many(<conditions>)

# 清空表
client.<dbname>.<tablename>.delete_many({})
```

## 条件函数

### 切片

skip、limit：参数为数字

```
# 只获取第n条开始到第m条结束的数据
client.<dbname>.<tablename>.find({}).skip(n).limit(m)
```

### 去重

distinct：参数为字符串格式的字段名！！！

```
# 以column字段为准进行去重
client.<dbname>.<tablename>.distinct(<column>)
```

### 排序

sort：参数为列表，列表元素需要是元组类型！！！

```
# 以column字段为准降序排序
client.<dbname>.<tablename>.find().sort([(<column>, -1)])
```



