---
title: sudo: unable to resolve host 172-16-200-220: Name or service not known
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

## 错误信息：

使用sudo的时候需要等待特别久

```
sudo: unable to resolve host 172-16-200-220: Name or service not known
```

## 原理：

配置静态主机名后没有登记到hosts列表里，每次sudo都是一个提权的过程，穿透至root环境，会涉及到DNS解析主机名的过程，如果更换了静态主机名却没有记录下，DNS无法解析只能等待timeout时间然后才能提权至localhost的超级权限

## 解决方法：

将静态主机名加入hosts列表里

```
sudo vim /etc/hosts

# 将主机名加到127.0.0.1后面
127.0.0.1	localhost <静态主机名>
```
