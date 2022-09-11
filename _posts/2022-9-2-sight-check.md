---
title: 1.124-sight接口联调
date: 2022-09-02 18:00:00 +0800
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

## 将接口写入前端

### 记录后端地址（@/utils/apis）

#### 加入景点模块的接口

```
/**
 * 景点模块的接口
 */
const SightApis = {
  // 景点列表
  sightListUrl: apiHost + '/sight/sight/list/'
}
```

#### 全局注册

```
export {
    ...
    SightApis
}
```

### 创建方法访问地址（@/components/home/HotBox）

引入异步请求库和已经写好的路由地址

```
<script>
import { ajax } from '@/utils/ajax'
import { SightApis } from '@/utils/apis'
</script>
```

创建访问函数(需要传递参数)

```
<script>
...

export default {
  ...
  methods: {
    /**
     * 获取热门景点数据
     */
    getDataList () {
      ajax.get(SightApis.sightListUrl, {
        params: {  // 传递GET参数
          is_hot: 1
        }
      }).then(({data}) => {  // 解构赋值
        this.dataList = data.objects
      })
    }
  },
  ...
}
</script>
```

调用函数

```
<script>
...

export default {
  ...
  created () {
    this.getDataList()
    ...
  }
}
</script>
```

校正模板中渲染的变量名

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
      is-link/>
    <!-- // 顶上导航 -->
    <!-- 景点列表 -->
    <div class="box-main">
      <a href="#" class="hot-item"
        v-for="item in dataList"
        :key="item.id">
        <div class="img">
          <span></span>
          <img :src="item.main_img" alt="">  // 修改
        </div>
        <h5 class="van-ellipsis">{{ item.name }}</h5>
        <div class="line-price">
          <span class="price">￥{{ item.min_price }}</span>起  //修改
        </div>
      </a>
    </div>
    <!-- // 景点列表 -->
  </div>
</template>
```

### 创建方法访问地址（@/components/home/FineBox）

引入异步请求库和已经写好的路由地址

```
<script>
import { ajax } from '@/utils/ajax'
import { SightApis } from '@/utils/apis'
</script>
```

创建访问函数(需要传递参数)

```
<script>
...

export default {
  ...
  methods: {
    /**
     * 获取精选景点数据
     */
    getDataList () {
      ajax.get(SightApis.sightListUrl, {
        params: {
          is_top: 1
        }
      }).then(({data}) => {
        this.dataList = data.objects
      })
    }
  },
  ...
}
</script>
```

调用函数

```
<script>
...

export default {
  ...
  created () {
    this.getDataList()
    ...
  }
}
</script>
```

校正子组件模板中渲染的变量名（@/components/common/ListSight.vue)

```
<template>
  <a href="#" class="sight-item">
    <!-- 左边的图片 -->
    <img :src="localItem.main_img" :alt="localItem.name">  // 修改
    <!-- 右边的文字 -->
    <div class="right">
      <h5>{{ localItem.name }}</h5>
      <van-rate
        readonly
        v-model="localItem.score"
        allow-half
        color="#ffd21e"
        void-icon="star"
        void-color="#eee"
      />
      <div class="tips">{{ localItem.comment_count}}人点评 | 100%满意</div>
      <div class="tips light">{{ localItem.province }}-{{localItem.city }}</div>  // 修改
      <div class="line-price">￥ {{ localItem.min_price }} 起</div>  // 修改
    </div>
  </a>
</template>
```

