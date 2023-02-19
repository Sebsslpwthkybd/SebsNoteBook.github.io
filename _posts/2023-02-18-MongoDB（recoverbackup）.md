---
title: MongoDB备份恢复
date: 2023-02-18 10:00:00 +0800
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

# 单表

## 导出

```
mongoexport --host=<host> --port=<port> -u <user> -p <password> --authenticationDatabase=<database to which user belongs> -d <database> -c <table> -f "_id,<column>" -o <filepath>
```

- 不指定-f 则备份内容为一条一条的记录
- <filepath>只能是csv或json的路径

## 导入

```
mongoimport --host=<host> --port=<port> -u <user> -p <password> --authenticationDatabase=<database to which user belongs> -d <database> -c <table> -f "_id,<column>" --file <filepath>
```

- 不指定-f 则备份内容为一条一条的记录
- <filepath>只能是csv或json的路径

# 逻辑库

## 导出

```
mongodump --host=<host> --port=<port> -u <user> -p <password> --authenticationDatabase=<database to which user belongs> -d <database> -o <folderpath>
```

- <folderpath>是存放文件的文件夹路径，会在指定文件夹路径下创建与导出逻辑库同名的文件夹

- --dumpDbUsersAndRoles 备份隶属于逻辑库的用户

- 数据以bson格式保存（二进制，不可直接查看），索引会以json形式记录

## 导入

```
mongorestore --host=<host> --port=<port> -u <user> -p <password> --authenticationDatabase=<database to which user belongs> -d <database> <databasepath>
```

- --drop 把现有数据删除后再全量导入
