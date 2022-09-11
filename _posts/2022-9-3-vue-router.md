---
title: 1.130-vue-router的引入
date: 2022-09-03 18:00:00 +0800
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

## 安装vue-router插件

```
npm install vue-router -S
```

## 配置路由规则（@/router/index.js）

### 使用vue-router

```
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

### 配置路由跳转规则（去到哪个组件）

```
const routes = [
  {
    path: '/',
    name: 'home',
    component: HomeView
  }
]
```

### 全域声明

```
const router = new VueRouter({
  routes
})

export default router
```

### main.js中使用vue-router

```
window.app = new Vue({
  router,  // 使用vue-router
  store,
  render: h => h(App)
}).$mount('#app')
```

## 路由的使用

### 组件渲染的位置(会自动替换并渲染)

```
<router-view></router-view>
```

### 模板中实现跳转（跳转触发器(a标签)）

```
<router-link to="/about"></router-link>
```

## 动态路由匹配

### 匹配参数

#### 设置动态匹配规则，参数为:name

```
const router = new VueRouter({
  routes: [
    { path: '/detail/:id', component:Detail }
  ]
})
```

#### 在组件中获取匹配参数和查询（/sight/list/456）

##### JavaScript中获取参数

```
let id = this.$route.params.id
```

##### 动态响应参数变化（监听）

```
watch: {
  $route(to, from){
  // 对路有变化作出响应
    this.id = this.$route.params.id
  },
  id (to, from){
  // 还可以监听data里的变量变化
  }
}
```

### 查询参数

#### 不需要设置动态匹配规则

#### 在组件中获取查询参数和查询（/sight/list/456?name=456）

##### JavaScript中获取参数

```
let name = this.$route.query.name
```

#####  获取多个查询参数（/sight/list/456?name=456&sort=asc）

JavaScript中获取参数

```
let name = this.$route.query.name
let sort = this.$route.query.sort
```

## 页面跳转

### 在模板中跳转

```
<template>
  <router-link to="/sight/sight/list"></router-link>
</template>
```

```
<template>
  <router-link :to='"/sight/" + id + "/" + types'></router-link>
</template>

<script>
  export default {
    data () {
      return {
        id: 456,
        types: 'type2'
      }
    }
  }
</script>
```

```
<template>
  <router-link :to="{ path:'/sight/' + id }"></router-link>
</template>

<script>
  export default {
    data () {
      return {
        id: 456
      }
    }
  }
</script>
```

```
<template>
  <router-link :to="{path: '/sight', query: { id: 'this.id'}}"></router-link>
</template>

<script>
  export default {
    data () {
      return {
        id: 456
      }
    }
  }
</script>
```

#### 路由命名

##### 取名字（@/router/index.js）

```
const routes = [
  {
    path: '/',
    name: 'home',  // 取名
    component: HomeView
  }
]
```

##### 按照名字跳转

```
<template>
  <router-link :to="{name:'home', params: {types: 'types2'}, query: {id: 'this.id'}}"></router-link>
</template>

<script>
  export default {
    data () {
      return {
        id: 456
      }
    }
  }
</script>
```

### 在JS中跳转（this.$router.push）

#### 跳转至指定页

```
<template>
  <a href="javascript:;" @click="goHome"></a>
</template>

<script>
  export default {
    methods: {
      goHome () {
        this.$router.push({name: 'home', params: {types: 'types2'}, query: {id: 'this.id'}})
      }
    }
  }
</script>
```

#### 页面前进或后退（this.$router.go）

```
this.$router.go(n)
```

##### 前进

```
this.$router.go(1)
```

##### 后退

```
this.$router.go(-1)
```

#### 替换浏览历史（替换当前页面并且不能后退）

```
this.$router.replace(location, onComplete?, onAbort?)
```

