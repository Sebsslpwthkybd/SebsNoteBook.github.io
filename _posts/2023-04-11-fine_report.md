---
title: 帆软学习
date: 2023-03-16 16:00:00 +0800
categories: [fine-report]
tags: [fine report]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

## 正常流程

```
1、	获取需求：主要细节的就是和谁提的 和谁一边改一边确认
2、	完成需求：
        帆软bi中导航栏-文件-切换工作目录-远程服务器2
        添加报表
3、	打开前台页面-左侧导航栏-管理系统
        目录管理-找到对应的目录下添加模板
```

## 在表中展示模板参数

```
=$参数名
```

## 准备数据

```
左下角模板数据集中，+-数据库查询-写sql
下方可以看到能获取到的字段名
拖动即可展示在表格上
```

## 数据项过滤条件

```
双击单元格-过滤
可选列 操作符 P（模板参数） 选择一个模板参数过滤
```

## 支持excel导入

```
导航栏-模板-模板web属性-填报页面设置
以下设置：为该模板单独设置
√使用工具栏
```

## 图表颜色

```
双击图表-右侧控件-单元格元素-特效
	添加条件-配色-系列序号-等于-（1， +∞）
```

## 选中栏目变黑了

```
导航栏-模板-模板web属性-填报页面设置
    以下设置：为该模板单独设置
    √填报当前编辑行背景设置：选颜色
```

## 图表切换

给图表添加 JavaScript 类型的超级链接，调用图表接口

```
FR.Chart.WebUtils.getChart(chartId).showIndexChart(index);
```

