---
title: 1.132-景点详情
date: 2022-09-07 18:00:00 +0800
categories: [Online_traveling]
tags: [django+vue]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

# 前端

## 新建景点详情各视图的文件夹（@/views/sight）

## 记录url（@/router/index.js）

### 引入视图

```
import SightList from '../views/sight/SightList.vue'
import SightDetail from '../views/sight/SightDetail.vue'
import SightInfo from '../views/sight/SightInfo.vue'
import SightComment from '../views/sight/SightComment.vue'
import SightImage from '../views/sight/SightImage.vue'
```

### 记录url

```
const routes = [
  ...
  // 景点列表
  {
    path: '/sight/list',
    name: 'SightList',
    component: SightList
  },
  // 景点详情
  {
    path: '/sight/detail/:id',
    name: 'SightDetail',
    component: SightDetail
  },
  // 景点介绍
  {
    path: '/sight/info/:id',
    name: 'SightInfo',
    component: SightInfo
  },
  // 评论列表
  {
    path: '/sight/comment/:id',
    name: 'SightComment',
    component: SightComment
  },
  // 景点图片
  {
    path: '/sight/image/:id',
    name: 'SightImage',
    component: SightImage
  }
]
```

## 实现跳转

### 景点列表

#### @/components/common/ListSight.vue

```
<template>
  <router-link class="sight-item"
    :to="{name: 'SightDetail', params: {id: item.id}}">
    ...
  </router-link>
</template>
```

#### @/components/home/HotBox.vue

```
<template>
  <!-- 热门景点 -->
  <div class="home-hot-box">
    <!-- 顶上导航 -->
    <van-cell
      title="热门推荐"
      icon="/static/home/hot/fire.png"
      value="全部榜单"
      title-style="text-align:left"
      is-link
      :to="{name: 'SightList', query: {name: '热门景点'}}"/>
    <!-- // 顶上导航 -->
    ...
    <!-- 景点列表 -->
    <div class="box-main">
      <router-link class="hot-item"
        v-for="item in dataList"
        :key="item.id"
        :to="{name: 'SightDetail', params: {id: item.id}}">
        ...
      </router-link>
    </div>
    <!-- // 景点列表 -->
  </div>
</template>
```

#### @/components/home/FineBox.vue

```
<template>
  <!-- 精选景点 -->
  <div class="home-fine-box">
    <!-- 顶上导航 -->
    <van-cell
      title="精选景点"
      icon="location-o"
      value="更多"
      title-style="text-align:left"
      is-link
      :to="{name: 'SightList', query: {name: '精选景点'}}"/>
    <!-- // 顶上导航 -->
    ...
  </div>
</template>
```

