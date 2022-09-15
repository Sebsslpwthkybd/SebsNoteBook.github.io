---
title: 1.132-景点详情（评论列表项组件）
date: 2022-09-11 16:00:00 +0800
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

## 评论列表项组件(@/component/sight/CommentItem)

### 1、VantUI中找组件

评分

```
<van-rate v-model="value" allow-half void-icon="star" void-color="#eee" />

export default {
  data() {
    return {
      value: 2.5,
    };
  },
};
```

图片

```
<van-image
  width="100"
  height="100"
  src="https://img01.yzcdn.cn/vant/cat.jpeg"
/>
```

图片预览

```
<van-image-preview v-model="show" :images="images" @change="onChange">
  <template v-slot:index>第{{ index }}页</template>
</van-image-preview>

export default {
  data() {
    return {
      show: false,
      index: 0,
      images: [
        'https://img01.yzcdn.cn/vant/apple-1.jpg',
        'https://img01.yzcdn.cn/vant/apple-2.jpg',
      ],
    };
  },
  methods: {
    onChange(index) {
      this.index = index;
    },
  },
};
```



### 2、模板

```
<template>
  <!-- 评论列表每一项 -->
  <div class="comment-item-box">
    ...
  </div>
</template>
```

#### 顶部评分和用户名

```
    <div class="cmt-header">
      <div class="rate">
        <van-rate v-model="value"
        allow-half
        void-icon="star"
        void-color="#eee"
        readonly />
      </div>
      <div class="user">张三*** 2020-1-1</div>
    </div>
```

##### 编写双向绑定的数据

```
<script>
export default {
  data() {
    return {
      value: 4.5,
    }
  }
}
</script>
```

#### 评论内容

```
    <div class="cmt-content">
      <p>评论内容评论内容评论内容评论内容评论内容评论内容评论内容评论内容</p>
    </div>
```

#### 图片列表

```
    <!-- 图片列表 -->
    <div class="cmt-imgs">
      <van-image
        width="100"
        height="100"
        src="https://img01.yzcdn.cn/vant/cat.jpeg"
      />
            <van-image
        width="100"
        height="100"
        src="https://img01.yzcdn.cn/vant/cat.jpeg"
      />
      <van-image
        width="100"
        height="100"
        src="https://img01.yzcdn.cn/vant/cat.jpeg"
      />
    </div>
    <!-- // 图片列表 -->
```

#### 图片预览

```
    <!-- 图片预览 -->
    <van-image-preview v-model="show" :images="images" @change="onChange">
      <template v-slot:index>第{{ index }}页</template>
    </van-image-preview>
```

### 3、样式

### 4、Home页面中引用轮播图组件(@/views/HomeView)

引入组件

```
<script>
...

// 评论项组件
import CommentItem from '@/components/sight/CommentItem'

export default {
  components: {
    CommentItem
  },
</script>
```

展示

```
    <!-- 用户评价 -->
    <div class="sight-comment">
	  ...
      <comment-item/>
      ...
    </div>
    <!-- // 用户评价 -->
```

### 5、填充数据

```
<script>
export default {
  data () {
    return {
      value: 4.5,
      show: false,
      index: 0,
      images: [
        'https://img01.yzcdn.cn/vant/apple-1.jpg',
        'https://img01.yzcdn.cn/vant/apple-2.jpg'
      ]
    }
  },
  methods: {
    onChange (index) {
      this.index = index
    }
  }
}
</script>
```

### 6、优化样式
