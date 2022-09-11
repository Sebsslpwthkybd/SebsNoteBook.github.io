---
title: 1.114-首页功能(景点列表组件开发)
date: 2022-08-23 18:00:00 +0800
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

## 景点列表组件(@/components/common/ListSight)

### 1、从精选经典组件中复制景点列表组件

#### 模板

```
<template>
  <a href="#" class="sight-item">
    <!-- 左边的图片 -->
    <img src="/static/home/hot/h1.jpg" alt="">
    <!-- 右边的文字 -->
    <div class="right">
      <h5>{{ localItem.name }}</h5>
      <van-rate
        readonly
        v-model="localItem.score"
        allow-half
        :size="25"
        color="#ffd21e"
        void-icon="star"
        void-color="#eee"
      />
      <div class="tips">4人点评 | 100%满意</div>
      <div class="tips light">广东-广州</div>
      <div class="line-price">￥ {{ localItem.price }} 起</div>
    </div>
  </a>
</template>
```

#### 样式

```
<style lang="less">
.sight-item {
  display: flex;
  margin-top: 10px;
  border-bottom: 1px solid #f6f6f6;  // 下划线
  img {
    width: 100px;
    height: 100px;
  }
  .right {
    text-align: left;  // 让文字向左对齐
    flex-grow: 1;  // 撑满整个区域
    padding-left: 5px;
    position: relative;  // 绝对定位

    h5 {
      color: #212121;
      font-size: 14px;
      padding: 5px 0;
      margin: 0;
    }
    .line-price {  // 绝对于整个容器定位
      position: absolute;
      right: 10px;
      top: 20px;
      display: inline-block;
      color: #f50;
      font-size: 16px;  // 字体大小
      font-weight: bold;  // 字体加粗
    }
    .tips {
      font-size: 12px;
      color: #666;
      &.light {  // 让下面的字体浅色一点
        color: #999;
      }
    }
  }
}
</style>
```

### 2、解决父子传值问题（props）

#### 子组件（@/components/common/ListSight）

只写props不把他变成本地变量的话eslint会报错说 Unexpected mutation of "item" prop（防止子组件修改父变量）

```
<script>
export default {
  props : ['item'],  // 与父组件传递的值不同名也没关系
  data: function () {  // 把他变成本地变量
    return {
      localItem: this.item
    }
  }
}
</script>
```

#### 父组件

##### import子组件

```
<script>
import SightItem from '@/components/common/ListSight'

export default {
  components: {
    SightItem
  },
  data () {
    return {
      dataList: []
    }
  },
}
```

模板中使用v-bind语法传递对象

```
<template>
  <sight-item
    v-for="item in dataList"  // 使用v-for多次复用
    :key="item.id"  // v-for指定key
    :item="item"/>  // 传递item值！！！
</template>
```

