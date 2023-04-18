---
title: linux用户管理
date: 2023-03-16 16:00:00 +0800
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

# linux用户管理

/etc/passwd文件

```
用户名:密码位(x):UID(五位数起步):GID(一般与UID相同):注释:用户目录:shell位置(/bin/bash)
```

# bashrc配置文件损坏或丢失

原版的bashrc配置文件位于

```
/etc/skel/.bashrc
```

恢复

```
cp /etc/skel/.bashrc ~/
```

```
source ~/.bashrc
```


