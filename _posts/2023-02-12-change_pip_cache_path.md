---
title: 更改pip缓存位置
date: 2023-2-12 10:00:00 +0800
categories: [python]
tags: [python]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

# 前言

由于pip cache默认存储路径为c盘，一段时间使用后占用内存已达16G。如果更改存储位置至机械硬盘，C盘爆满的问题会得到有效缓解

# 方法

机械硬盘中创建缓存目录

```
D:\code\pipcache
```

配置新的pip cache路径

```
pip config set global.cache-dir "D:\code\pipcache"
```

删除C盘pip cache路径

```
C:\Users\LEEsongHoU\AppData\Local\pip
```

