---
title: 1.132-景点详情（评论列表部分）
date: 2022-09-11 18:00:00 +0800
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
<van-cell title="单元格" icon="location-o" />
```

导航栏图标

```
<van-icon name="comment-o" />
```

### 2、模板

```
    <!-- 用户评价 -->
    <div class="sight-comment">
      ...
    </div>
    <!-- // 用户评价 -->
```

#### 顶部导航栏

```
      <van-cell title="热门评论"
      icon="comment-o"
      title-style="text-align:left" />
```

#### 评论列表

单独写成一个组件（@/component/sight/CommentItem）

#### 查看更多

```
      <router-link :to="{name:'SightComment', params: {id}}">
        查看更多
      </router-link>
```

#### 编写获取id的方法

```
<script>
export default {
  data () {
    return{
      id: ''
    }
  },
  created () {
    this.id = this.$route.params.id
  }
}
</script>
```

### 3、样式

#### 评论列表（跟上面间隔一个空槽、背景颜色改为白色）

```
  // 评论列表
  .sight-comment {
    margin-top: 10px;
    background-color: #fff;
  }
```

#### 查看更多（将行列元素转换为块级元素）

```
  // 查看更多
  .link-more {
    display: block;  // 将行列元素转换为块级元素
    color: #666;
    padding: 10px;
  }
```

