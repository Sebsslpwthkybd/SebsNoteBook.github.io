---
title: MongoDB修改操作
date: 2023-2-15 10:00:00 +0800
categories: [MongoDB]
tags: [MongoDB]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

# 修改记录

update()：修改单条记录

updateMany()：修改多条记录

```
db.<tablename>.updateMany({condition}, {$set:{data}})
```

## 规则

condition可以为空，但必须要写成json格式

## 例子

```
# 将李强一个人的年龄设为26，班级号设为2-6
db.student.update({name: "李强"}, {$set: {age: 26, classno: "2-6"}})
```

```
# 将大于25岁的男生们分入2-6班
db.student.updateMany({sex: "男"， age: {$gte: 25}}, {$set: {classno: "2-6"}})
```

## 加法运算

$inc：对某个字段进行加法运算

```
# 对每条记录的age字段的值+2（不存在该字段的记录就视为0+2）
db.student.updateMany({}, {$inc: {age: 2}})
```

## 添加元素

$push： 向数组属性添加元素

### 例子

```
# 去除Jack老师的教务主任职务
db.teacher.update({name: "Jack"}, {$push: {role: "教务主任"}})
```

## 删除元素

$pull：删除数组属性的元素

### 例子

```
# 任命Jack老师为副校长
db.teacher.update({name: "Jack"}, {$pull: {role: "副校长"}})
```

# 删除记录

## 删除字段

$unset

### 例子

```
# 删除所有记录中的city和tel字段
db.student.updateMany({}, {$unset: {city: 1, tel: 1}})
```

## 删除整条记录

remove()

### 例子

无条件删除

```
# 删除整个表内的数据
db.student.remove({})
```

有条件删除

```
# 删除6-2班所有男生的数据
db.student.remove({class: "6-2", sex: "男"})
```

