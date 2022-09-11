---
title: 1.123-system接口联调
date: 2022-08-30 18:00:00 +0800
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

#### 加入固定的地址前缀

```
const apiHost = 'http://127.0.0.1:8000'
```

为了解决跨域问题，已经在vue.config.js中进行了改写，访问后端接口可以使用（/api 开头的链接会自动改写为http://127.0.0.1:8000）

```
const apiHost = 'http://127.0.0.1:8080/api'
```

#### 加入系统模块的接口

```
/**
 * 系统模块的接口
 */
const SystemApis = {
  // 轮播图列表
  sliderListUrl: apiHost + '/system/slider/list/'
}
```

#### 全局注册

```
export {
    ...
    SystemApis
}
```

### 创建方法访问地址（@/components/home/BannerBox）

引入异步请求库和已经写好的路由地址

```
<script>
import { ajax } from '@/utils/ajax'
import { SystemApis } from '@/utils/apis'
</script>
```

创建访问函数

```
<script>
...

export default {
  ...
  methods: {
    /**
     * 获取轮播图数据
     */
    getDataList () {
      ajax.get(SystemApis.sliderListUrl).then(res => {
        console.log('res:', res)
        this.bannerList = res.data.objects  // 获取
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
<!-- 首页轮播图 -->
  <div class="home-banner-box">
    <van-swipe class="my-swipe" :autoplay="3000" indicator-color="white">
      <van-swipe-item v-for="item in bannerList"
        :key="item.id">
        <img :src="item.img_url" alt="">  // 与传输一致
      </van-swipe-item>
    </van-swipe>
  </div>
</template>
```

# 后端

加入媒体url(settings.py)使从后端获取的图片url自动填充成全路径

```
MEDIA_URL = 'http://localhost:8080'
```

