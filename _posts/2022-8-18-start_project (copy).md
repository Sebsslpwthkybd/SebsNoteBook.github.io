---
 title: 1.10-创建项目
date: 2022-08-18 18:00:00 +0800
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

```
vue create traveling_vue
```

![选择 npm config get registry 2022_8_18 周四 17_38_33](/../Sebsslpwthkybd.github.io/_posts/2022-8-1-start_project.assets/选择 npm config get registry 2022_8_18 周四 17_38_33-16608159928027.png)

![npm config get registry 2022_8_18 周四 17_39_15](/../Sebsslpwthkybd.github.io/_posts/2022-8-1-start_project.assets/npm config get registry 2022_8_18 周四 17_39_15-16608159955919.png)

### /src

#### 	./assets

​		./style  // 全局样式

```
// App.vue中使用

<style lang="less">
// 公共样式
@import "./assets/style/common.less"; 
</style>
```

#### ./components  // 组件库

​		./common  // 可复用的全局组件

​		./...  // 其他组件

#### 	./utils

​		./apis.js  // 全局接口

​		./constans.js  // 全局常量

​		./filters.js  // 全局注册过滤器

```
// main.js中注册

import * as filters from './utils/filters'

// 注册过滤器
Object.keys(filters).forEach(k => Vue.filter(k, filters[k]))
```

实现全局引用VantUI库

### main.js

```
import * as filters from './utils/filters'
// VantUI组件库
import Vant from 'vant'
import 'vant/lib/index.css'

// VantUI组件库
Vue.use(Vant)

Vue.config.productionTip = false

// 注册过滤器
Object.keys(filters).forEach(k => Vue.filter(k, filters[k]))
```

关闭eslint语法对于{ }内空格的限制

package.json

```
  "eslintConfig": {
      "rules": {
      "object-curly-spacing": 0
    }
  },
```

## Django

### 设计数据库模型

系统模块：轮播图、用户反馈

景点模块：景点、景点详情、景点评论

用户模块：用户、用户详细信息、登陆历史

订单模块：订单、订单明细、支付相关

### 连接数据库（traveling_django/settings.py）

```
DATABASES = {
    # 'default': {
    #     'ENGINE': 'django.db.backends.sqlite3',
    #     'NAME': BASE_DIR / 'db.sqlite3',
    # }
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'traveling',
        'USER': 'root',
        'PASSWORD': 'Paul34252780*',
        'HOST': '119.91.211.91',
        'PORT': '3306',
    }
}
```

