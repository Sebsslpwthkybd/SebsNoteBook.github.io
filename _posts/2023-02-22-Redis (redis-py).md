---
title: redis-py连接redis
date: 2023-02-22 16:00:00 +0800
categories: [NoSQL]
tags: [Redis]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

# 下载

pip

```
pip install redis -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```

详细信息

```
https://pypi.org/project/redis/
```

# 创建链接

连接对象

```
import redis

r = redis.Redis(
    host=<host>,
    port=6379,
    password=<password>,
    db=0  # 不能切换，需要重新创建连接池
)
```

# 创建连接池

```
import redis

pool = redis.ConnectionPool(
    host=<host>,
    port=6379,
    password=<password>,
    db=0,  # 不能切换，需要重新创建连接池
    max_connections=20
)

```

创建连接

```
r = redis.Redis(
	connection_pool=pool	
)
```

关闭连接（python垃圾回收时会自动归还连接）

```
del r
```

# 操作

## 字符串

增

```
# 单个增
try:
	r.set(<key>,<value>)
except Exception as e:
	print(e)
finally:
	del r

# 批量增
try:
	r.mset({<key1>: <value1>, <key2>: <value2>, ...})
except Exception as e:
	print(e)
finally:
	del r
```

删

```
try:
	r.delete(<key1>, <key2>, ...)
except Exception as e:
	print(e)
finally:
	del r
```

查

```
# 单个查 (由于需要decode，查不到值会报错)
try:
	value = r.get(<key>).decode("utf-8")
except AttributeError as e:
	print("Counld not found value from key {}!".format(<key>))
finally:
	del r

# 批量查 (由于需要decode，查不到值会报错)
try:
	result=r.mget(<key1>, <key2>, ...)
	for one in result:
		res.append(one.decode("utf-8"))
except AttributeError as e:
	print("Counld not found value from key {}!".format(<key>))
finally:
	del r
```

## 列表

增

```
try:
	r.rpush(<key>, <value1>, <value2>, ...)
except Exception as e:
	print(e)
finally:
	del r
```

删

```
try:
	r.lpop(<key>)
except Exception as e:
	print(e)
finally:
	del r
```

查

```
try:
	res = r.lrange(<key>, 0, -1)
	for one in res:
		result.append(one.decode("utf-8"))
except Exception as e:
	print(e)
finally:
	del r
```

## 集合

增

```
try:
	r.sadd(<key>, <value1>, <value2>, ...)
except Exception as e:
	print(e)
finally:
	del r
```

删

```
try:
	r.srem(<key>, <value>)
except Exception as e:
	print(e)
finally:
	del r
```

查

```
try:
	res = r.smembers(<key>)
except Exception as e:
	print(e)
finally:
	del r
```

## 有序集合

增

```
# 增加元素
try:
	r.zadd(<key>, {<value1>: <score1>, <value2>: <score2>, ...})
except Exception as e:
	print(e)
finally:
	del r
	
# 元素分数递增
try:
	r.zincrby(<key>, <incr_score>, <value1>)
except Exception as e:
	print(e)
finally:
	del r
```

查

```
# 降序排序
try:
	res = r.zrevrange(<key>, 0, -1)
	for one in res:
		result.append(one.decode("utf-8"))
except Exception as e:
	print(e)
finally:
	del r
```

## 哈希表

增

```
try:
	r.hmset(<key>, {<hashkey1>: <hashvalue1>, <hashkey2>: <hashvalue2>, ...})
except Exception as e:
	print(e)
finally:
	del r
```

删

```
try:
	r.hdel(<key>, <hashkey>)
except Exception as e:
	print(e)
finally:
	del r
```

改

```
try:
	r.hset(<key>, <hashkey>, <hashvalue>)
except Exception as e:
	print(e)
finally:
	del r
```

查

```
# 全部元素
try:
	res = r.hgetall(<key>)  # 元组的集合
	for one in res:
		result.append(one.decode("utf-8"), result[one].decode("utf-8"))  # 属性的值需要单独提取
except Exception as e:
	print(e)
finally:
	del r

# 是否存在
r.hexists(<key>, <hashkey>)
```

# 事务

redis-py通过pipeline方式向Redis服务器传递批处理命令和执行事务

## 创建pipeline对象

```
pipline = r.pipeline()
```

## 监听

```
pipline.watch(...)
```

## 开启事务编辑

```
pipline.multi()
```

！！！所有的操作必须使用pipline对象的方法（不要继续使用 r.hmset之类的）

## 提交

```
pipline.execute()
```

## 关闭pipeline（很重要，不关闭无法回收至连接池）

```
pipline.reset()
```

例

```
import redis


pool = redis.ConnectionPool(
    host=<host>,
    port=6379,
    password=<password>,
    db=0,  # 不能切换，需要重新创建连接池
    max_connections=20
)
r = redis.Redis(
	connection_pool=pool	
)

try:
	pipline=r.pipeline()
	pipline.watch(...)
	pipline.multi()
	...
	pipline.execute()
except Exception as e:
	print(e)
finally:
	if "pipline" in dir():
		pipline.reset()
	del r
```

