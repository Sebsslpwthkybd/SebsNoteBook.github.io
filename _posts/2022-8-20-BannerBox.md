---
title: 1.111-首页功能(轮播图组件开发)
date: 2022-08-20 18:00:00 +0800
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

## 轮播图组件(@/home/BannerBox)

在VantUI的展示组件中找到轮播图

```
<van-swipe class="my-swipe" :autoplay="3000" indicator-color="white">
  <van-swipe-item>1</van-swipe-item>
  <van-swipe-item>2</van-swipe-item>
  <van-swipe-item>3</van-swipe-item>
  <van-swipe-item>4</van-swipe-item>
</van-swipe>
```

data里生成一个用于盛放轮播图路径的对象bannerList

```
<script>
export default {
  data () {
    return {
      bannerList: []
    }
  },
  methods: {

  },
```

直接写定对象数据

```
  created () {
    this.bannerList = [
      { id: 1, img: '/static/home/banner/banner1.jpg' },
      { id: 2, img: '/static/home/banner/banner2.jpg' },
      { id: 3, img: '/static/home/banner/banner3.jpg' }
    ]
  }
}
</script>
```

放入BannerBox.vue中，并将要轮播的图片换成素材库里的图片（使用v-for遍历bannerList对象以获得所有的轮播图）

```
<template>
<!-- 首页轮播图 -->
  <div class="home-banner-box">
    <van-swipe class="my-swipe" :autoplay="3000" indicator-color="white">
     <van-swipe-item v-for="item in bannerList"
        :key="item.id">
        <img :src="item.img" alt="">
    </van-swipe>
  </div>
</template>
```

### Home页面中引用轮播图组件(@/views/HomeView)

引入组件

```
<script>
// banner
import Banner from '@/components/home/BannerBox'

export default {
  name: 'HomeView',
  components: {
    // banner
    Banner
  }
}
</script>
```

展示

```
<template>
  <div class="home">
    <!-- banner -->
    <Banner/>
  </div>
</template>
```

### 优化样式

轮播图占比空间太大

```
<style lang="less">
.home-banner-box {
  img {
    width: 100%;  //填满左右两边
    height: auto;  // 上下大小自适应
  }
}
</style>
```

# 后端

## 设计轮播图ORM模型

### 1、分析并设计数据库模型



### 2、编码ORM模型

### 3、检测模型

### 4、模型同步
