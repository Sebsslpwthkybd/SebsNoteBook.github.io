---
title: 1.132-景点详情（门票列表部分）
date: 2022-09-11 14:00:00 +0800
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
<van-icon name="bookmark-o" />
```

门票列表闹钟图标

```
<van-icon name="clock-o" />
```

门票列表标签

```
<van-tag mark type="primary">标签</van-tag>
```

预订按钮

```
<van-button type="warning">警告按钮</van-button>
```

### 2、模板

```
    <!-- 门票列表 -->
    <div class="sight-ticket">
	  ...
    </div>
    <!-- // 门票列表 -->
```

上方的导航栏

```
      <van-cell title="门票"
      icon="bookmark-o"
      title-style="text-align:left" />
```

下方的门票列表

```
      <div class="ticket-item"  v-for="i in 5" :key="i">
        ...
      </div>
```

左半部分

```
        <div class="left">
          <div class="title">成人票</div>
          <div class="tips">
            <van-icon name="clock-o" />
            <span>7点之前可以预定</span>
          </div>
          <div class="tags">
            <van-tag mark type="primary">标签1</van-tag>
          </div>
        </div>
```

右半部分

```
        <div class="right">
          <div class="price">
            <span>￥</span>
            <strong>65</strong>
          </div>
          <router-link to="#">
            <van-button type="warning" size="small">预订</van-button>
          </router-link>
        </div>
```

#### 3、样式

```
  // 门票列表
  .sight-ticket {
    margin-top: 10px;  // 空槽
    background-color: #fff;
    ...
  }
```

##### 门票（盒模型布局、背景色白色、与下方有空槽）

```
    .ticket-item {
      display: flex;
      border-bottom: 1px solid #f6f6f6;
      padding-bottom: 10px;  // 空槽
      ....
    }
```

##### 左半部分（左对齐、上下5像素， 左右10像素）

```
      .left {
        flex: 1;
        text-align: left;
        padding: 5px 10px;
        .title {
          padding: 5px 0;
        }
        .tips {
          font-size: 12px;
        }
      }
```

##### 右半部分

```
      .right {
        width: 100px;
        .price {
          color: #ff9800;
          strong {
            font-size: 20px;
          }
        }
      }
```

