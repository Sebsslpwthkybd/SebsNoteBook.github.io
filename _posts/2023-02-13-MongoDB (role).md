---
title: MongoDB角色管理
date: 2023-02-13 10:00:00 +0800
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

# 内置角色

指定逻辑库（不是所有逻辑库都有权力）

```
Read  # 只读
readWrite  # 读写
dbAdmin  # 管理
userAdmin  # 对用户管理
```

所有逻辑库（角色只能创建在admin逻辑库中）

```
readAnyDatabase  # 只读
readWriteAnyDatabase  # 读写
dbAdminAnyDatabase  # 管理
userAdminAnyDatabase  # 对用户管理
```

```
clusterAdmin  # 管理mongo集群
root  # 最高权限
```

# 设置登陆账户

```
use admin
```

```
db.createUser({
	user: "admin",  # 角色登录名（叫什么不影响）
	pwd: "...",
	roles: [{role: "root", db: "admin"}]  # 指定角色和角色所属的逻辑库
})
```

# 开启登陆验证

- 创建配置文件（C:\Program Files\MongoDB\Server\4.0\bin\mongo.cnf）

```
dbpath=<data path>
logpath=<log path>  # mongod.log文件的绝对路径
auth=True
```

- 重新安装MongoDB服务

```
mongod --config "C:\Program Files\MongoDB\Server\4.0\bin\mongo.cnf" --reinstall
```

# 登录验证

- 图形界面

```
直接使用用户密码登录（之前没有使用用户密码的链接会失效）
```

- 命令行

登陆验证设置在use database阶段

```
use admin
db.auth(<user>, <password>)  # 返回1代表成功
```

