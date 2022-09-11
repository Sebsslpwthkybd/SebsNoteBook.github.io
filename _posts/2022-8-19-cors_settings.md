---
title: 1.10-跨域问题的解决
date: 2022-08-19 18:00:00 +0800
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

## Vue

重定向（vue.config.js）

```
// Node.js里的模块
module.exports = {
  devServer: {
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true,
        pathRewrite: {
          '^api': ''
        }
      }
    }
  }
}
```

自定义请求头和携带上一次cookie（@/utils/ajax）

```
import axios from "axios"

export const ajax = axios.create({
    headers: {
        source: 'h5',  // 请求头1
        icode: 'acbd',  // 请求头2
        'Content-Type': 'application/x-www-form-urlencoded'
    },
    withCredentials: true  // 是否携带上一次的cookie
})
```

异常处理

```
ajax.interceptors.request.use(function (config) {
    // 发送请求之前做什么
    console.log('请求拦截到了')
    return config
}, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error)
})

ajax.interceptors.request.use(function (response) {
    // 对响应数据做点什么
    console.log('相应拦截到了')
    return response
}, function (error) {
    if (error.response.status === 401) {
        window.alert('未登录， 即将跳转到登陆页面')
    } else if (error.response.status === 500) {
        window.alert('服务器正忙，请稍后')
    }
    return Promise.reject(error)
})
```

将Vue赋值给window.app(main.js)

```
window.app = new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```

## Django

安装第三方扩展

```
pip install django-cors-headers
```

### 配置(settings.py)

引用第三方扩展

```
INSTALLED_APPS = [
	'corsheaders',
]

MIDDLEWARE = [
	'django.contrib.sessions.middleware.SessionMiddleware',
    'corsheaders.middleware.CorsMiddleware',  // 在两者的中间添加
    'django.middleware.common.CommonMiddleware',
]
```

##### CORS跨域配置

请求白名单

```
CORS_ORIGIN_WHITELIST = (
    'http://127.0.0.1:8080',
    'http://localhost:8080'
)
```

请求头和cookie

```
# 允许携带哪些请求头
CORS_ALLOW_HEADERS = ('source', 'icode')
# 允许携带cookie
CORS_ALLOW_CREDENTIALS = True
```

