---
title: Redis事务
date: 2023-02-21 11:00:00 +0800
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

# 抛弃认知

Redis事务机制不具备sql数据的undo、redo日志操作，不能保证事务的原子性

A成功了，B执行时宕机，表现即为A成功，B失败

# Redis事务机制（批处理执行）

## 为了避免命令被插队

Redis是异步单线程执行，一个线程即对应所有的客户端，哪个客户端上传了命令，线程就会执行，所以不能保证一个客户端的多个命令，不会被其他客户端的命令插队。

## 只能保证ACID属性里的一致性和隔离性

### 一致性（保证命令串行执行）

开启事务前要用WATCH命令监视要操作的记录

```
WATCH key1 key2 ...
```

## 开启事务

```
MULTI
...
EXEC
```

- 开启事务后所有的操作都不会立即执行，只有等到EXEC命令的时候才会批处理执行
- 只有对应记录 watch后exec前 没有被修改过，才能执行成功
- exec后没有提示则为失败

## 取消事务

```
MULTI
...
DISCARD
```

- 只是清空缓存内的批处理命令
- 事务没exec前可以撤销，exec后不可撤销
