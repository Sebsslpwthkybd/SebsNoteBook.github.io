---
title: Redis数据结构
date: 2023-02-21 16:00:00 +0800
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

# 数据结构

## key类型

字符串

- ### 删除

```
DEL key
```

- ### 查询

```
# key是否存在
EXISTS key

# value数据类型
TYPE key
```

- ### 设置记录过期时间

```
# n秒后
EXPIRE key n

# 指定时间
EXPIREAT key (UNIX时间戳)
```

- ### 移除过期时间

```
PERSIST key
```

- ### 修改

```
# 迁移记录到另一个逻辑库n
MOVE key n

# 重命名
RENAME key newKeyName
```



## value类型

字符串

哈希

列表

集合

### 字符串（string）

- 用途：保存文字、序列化的二进制数据
- 存储容量：最大512M

```
# 存储
SET key string

# 获取
GET key

# 删除
DEL key
```

- 字符串指令

字符串切片（n：起始位置，m：偏移量）

```
GETRANGE key n m
```

字符串长度

```
STRLEN key
```

设置带有过期时间的key-value（time单位：秒）

```
SETEX key time value

# 单位为毫秒
PSETEX key time value
```

设置多个key-value

```
MSET key1 value1 key2 value2
```

获得多个value

```
MGET key1 key2
```

向字符串结尾追加内容

```
APPEND key content
```

- 字符串数字操作

自增1

```
INCR key
```

增加n

```
INCRBY key n
```

增加浮点数m

```
INCRBYFLOAT key m
```

自减1

```
DECR key
```

减n

```
DECRBY key n
```

### 哈希（HASH）

- 用途：保存复杂的结构化数据
- 存储

```
# 设置哈希表单个属性
HSET key structure  # structure：key value 格式

# 追加属性
HSET key structure

# 设置多个属性
HMSET key structure1 structure2 ...

```

- 查询

```
# 查询一个字段的值
HGET key structurekey

# 查询多个字段的值
HMGET key structurekey1 structurekey2 ...

# 获得所有的字段
HGETALL key

# 获得所有字段的名称
HKEYS key

# 获得字段的数量
HLEN key

# 判断是否存在某个字段
HEXISTS key structurekey

# 获得所有字段的值
HVALS key
```

- 删除

```
# 删除字段
HDEL key structurekey1 structurekey2 ...
```

- 修改

```
# 追加整数num值
HINCRBY key structurekey num

# 追加浮点数num值
HINCRBYFLOAT key structurekey num
```

### 列表（LIST）

- 用途：保存序列化数据

- 存储

```
# 向右侧添加元素
RPUSH key value1 value2 ...  # right push

# 向左侧添加元素
LPUSH key value1 value2 ...  # left push

# 向value前插入元素newValue
LINSERT key BEFORE value newValue

# 向value后插入元素newValue
LINSERT key AFTER value newValue
```

- 修改

```
# 将位于index的值修改为newValue
LSET key index newValue  # list set
```

- 查询

```
# 切片，index1为开始序号，index2为结束序号
LRANGE key index1 index2  # list range

# 获得列表长度
LLEN key  # list length

# 获得列表位于index的元素
LINDEX key index
```

- 删除

```
# 删除最左侧元素
LPOP key

# 删除最右侧元素
RPOP key

# 删除列表n个指定元素value，如果有m个，则删除m个里的前n个
LREM key n value

# 删除整个列表
DEL key
```

### 集合（SET）

- 用途：需要列表中的元素不重复的时候
- 存储

```
# 添加单个值
SADD key value

# 批量添加
SADD key value1 value2 ...

# 如果添加重复的值，redis只会表示插入失败
```

- 查询

```
# 直接返回集合里所有的值
SMEMBERS key  # 集合没有索引序号，最早插入的值不一定在开头

# 获取集合长度
SCARD key

# 判断是否有某个元素
SISMEMBER key value

# 随机获取n个元素
SRANDMEMBER key n
```

- 删除

```
# 删除指定值
SREM key value1 value2 ...

# 删除集合里所有数据
DEL key

# 随即删除并返回某个元素
SPOP key
```

比较元素是否相同原理：

不是通过一个个值比对，而是直接比对区间内的哈希值是否一致就行了（超快速），所以集合是按照哈希值排序的，早插入的值不一定就在前面

### 有序集合

- 用途：集合元素除了元素值外，还需要有分数值，可以按照分数值大小对数据进行排序（热搜榜词条的排序，用户对其进行搜索即自动增加，不需程序维护），多用于持久化的多线程计数

- 增加

```
ZADD key score1 value1 score2 value2 ...
```

- 查询

```
# 降序排列切片[n1, n2]的元素(全部为0 -1)
ZREVRANGE key n1 n2

# 升序排列切片[n1, n2]的元素(全部为0 -1)
ZRANGE key n1 n2

# 获取有序集合长度
ZCARD key

# 查询[n1, n2]分数值区间内的元素数量
ZCOUNT key n1 n2

# 查询value元素的分数值
ZSCORE key value
```

```
# 获得元素的升序排名(从0开始)
ZRANK key value

# 获得元素的降序排名(从0开始)
ZREVRANK key value
```

```
# 获取 [score1,score2] 分数值区间内的集合内容
ZRANGEBYSCORE key score1 score2  # （升序）
ZREVRANGEBYSCORE key score2 score1  # （降序）

# 获取 [score1,score2) 分数值区间内的集合内容
ZRANGEBYSCORE key score1 (score2  # （升序）
ZREVRANGEBYSCORE key (score2 score1  # （降序）

# 获取[score,+∞)分数值的集合内容
ZRANGEBYSCORE key score +inf  # （升序）
ZREVRANGEBYSCORE key +inf score  # （降序）

# 获取(-∞,score]分数值的集合内容
ZRANGEBYSCORE key -inf score  # （升序）
ZREVRANGEBYSCORE key score -inf  # （降序）
```

- 修改

```
# 某个值的分数增加n
ZINCRBY key n value
```

- 删除

```
# 删除整个有序集合
DEL key

# 删除有序集合中的元素
ZREM key value1 value2 ...

# 删除排名n1~n2区间内的元素
ZREMRANGEBYRANK key n1 n2

# 删除分数值区间内的元素
ZREMRANGEBYSCORE key -inf (score
```

