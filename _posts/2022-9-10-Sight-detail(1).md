---
title: 1.132-景点详情(景点大图部分)
date: 2022-08-22 18:00:00 +0800
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

## 精选景点组件(@/views/sight/SightDetail)

### 1、VantUI中找组件

顶部导航栏

```
<van-nav-bar
  title="标题"
  left-text="返回"
  right-text="按钮"
  left-arrow
  @click-left="onClickLeft"
  @click-right="onClickRight"
/>
```

景点图片

```
<van-image
  width="10rem"
  height="10rem"
  fit="contain"
  src="https://img01.yzcdn.cn/vant/cat.jpeg"
/>
```

景点图片的小图标

```
<van-icon name="video-o" />
```

地址栏

```
<van-cell title="单元格" icon="shop-o">
  <!-- 使用 right-icon 插槽来自定义右侧图标 -->
  <template #right-icon>
    <van-icon name="search" class="search-icon" />
  </template>
</van-cell>
```

地址栏左侧小图标

```
<van-icon name="location-o" />
```

地址栏右侧小图标

```
<van-icon name="arrow" />
```

### 2、模板

#### 头部导航栏

```
<template>
  <!-- 景点详情 -->
  <div class="page-sight-detail">
    <!-- 页面头部 -->
    <van-nav-bar
      left-text="返回"
      left-arrow
      @click-left="onClickLeft"
      fixed
    />
</div>
</template>
```

##### 返回事件

```
<script>
export default {
  methods: {
    goBack () {
      this.$router.go(-1)
    }
  }
}
</script>
```

#### 景点大图

```
    <!-- 大图 -->
    <div class="sight-banner">
		...
    </div>
    <!-- // 大图 -->
```

##### 图片

```
      <van-image src="/stati/home/hot/h1_max.jpg"
      width="100%"
      height="100%"/>
```

##### 提示

```
      <div class="tips">
        <router-link class="pic-sts" to="{name: 'SightImgae' params: {id:'123'}}">
          <van-icon name="video-o" />
          <span>10 图片</span>
        </router-link>
        <div class="title">景点标题</div>
      </div>
```

#### 评分、景点介绍

```
    <!-- 评分、景点介绍 -->
    <div class="sight-info">
     ...
    </div>
    <!-- // 评分、景点介绍 -->
```

##### 左半部分

```
      <div class="left">
      	...
      </div>
```

###### 评分

```
        <div class="info-title">
          <strong>5分</strong>
          <small>很棒</small>
        </div>
```

###### 评论

```
        <div class="info-tips">50 评论</div>
```

###### 跳转箭头

```
        <van-icon name="arrow" />
```

##### 右半部分

```
      <div class="right">
      	...
      </div>
```

###### 景点介绍

```
        <div class="info-title">
          <span>景点介绍</span>
        </div>
```

###### 开放时间

```
        <div class="info-tips">开放时间、贴士</div>
```

###### 跳转箭头

```
        <van-icon name="arrow" />
```

#### 地址栏

```
    <!-- 地址信息 -->
    <van-cell title="广东省广州市番禺区番禺大道"
    icon="location-o"
    :title-style="{'text-align': 'left'}"  // 左对齐
    is-link  // 使用vue-router
    >
      <!-- 使用 right-icon 插槽来自定义右侧图标 -->
      <template #right-icon>
        <van-icon name="arrow" />
      </template>
    </van-cell>
```

#### 3、样式

```
<style lang="less">
.page-sight-detail {
  ...
}
</style>

```

##### 景点大图

```
  // 景点大图
  .sight-banner {
    position: relative;
  }
```

###### 大图下面的图片数量提示和景点标题（绝对定位）

```
    .tips {
      position: absolute;
      left: 10px;
      bottom: 10px;
      font-size: 16px;
      color: #fff;
      .pic-sts {
        color: #fff;
        border-radius: 30px;
        font-size: 14px;
        background-color: rgba(0,0,0,0.4);
      }
    }
```

##### 顶部导航栏（透明）

```
  .van-nav-bar {
    background-color: transparent;
  }
```

##### 评分和景点介绍（有底部线框、盒状模型的左右结构）

```
  // 评分、景点介绍
  .sight-info {
    display: flex;
    background-color: #fff;
    border-bottom: 1px solid #f6f6f6;
  	...
  }
```

右边框会有最左线框分割左右两个盒子

```
    & > div {
      flex: 1;
      position: relative;
    }
    .right {
      border-left: 1px solid #f6f6f6;
    }
```

主要文字（左对齐、评分里分数要更改颜色）

```
    .info-title {
      text-align: left;
      padding: 5px 10px;
      strong {
        color: #ff8300;
      }
    }
```

主要文字下方的小提示（左对齐、浅灰色，缩小字体）

```
    .info-tips {
      color: #999;
      font-size: 12px;  // 字体大小
      text-align: left;
      padding: 5px 10px;
    }
```

最右侧的跳转图标（重写、使用绝对定位放在最右侧上方）

```
    .van-icon {
      position: absolute;
      right: 5px;
      top: 5px;
    }
```

