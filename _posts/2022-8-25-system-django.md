---
title: 1.121-system模块(轮播图接口开发)
date: 2022-08-24 18:00:00 +0800
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

# 后端

## system模块

### 1、创建system模块

```
python manage.py startapp system
```

### 2、添加进INSTALL_APPS(traveling_django/settings.py)

```
INSTALLED_APPS = [
	...
    'system.apps.SystemConfig',
]

```

### 3、ORM模型

#### ORM模型设计（system/models.py）

轮播图模型

```
from django.db import models


# Create your models here.
class Slider(models.Model):
    name = models.CharField('名称', max_length=32)
    desc = models.CharField('描述', max_length=100, null=True, blank=True)
    types = models.SmallIntegerField('展现的位置', default=10)
    img = models.ImageField('图片地址', max_length=255, upload_to='%Y%m/slider')
    reorder = models.SmallIntegerField('排序字段', default=0, help_text='数字越大越靠前')
    start_time = models.DateTimeField('生效开始时间', null=True, blank=True)
    end_time = models.DateTimeField('生效开始时间', null=True, blank=True)
    target_url = models.CharField('跳转的地址', max_length=100, null=True, blank=True)
    is_valid = models.BooleanField('是否有效', default=True)
    created_at = models.DateTimeField('创建时间', auto_now_add=True)
    updated_at = models.DateTimeField('修改时间', auto_now=True)

    class Meta:
        db_table = 'system_slider'
        ordering = ['-reorder']

```

#### 同步模型

检查语法

```
python manage.py check
```

生成同步原语

```
python manage.py makemigrations
```

同步模型至数据库

```
 python manage.py migrate
```

### 4、接口实现

#### 4.1、设计接口返回标准

##### 定义接口返回结构

```
{
meta: 源数据
objects: 数据列表（数组）
}
```

##### 接口错误信息约定（RESTful）

GET

```
安全且幂等
获取表示
变更时获取表示（缓存）
200（OK） - 表示已在响应中发出
204（无内容） - 资源有空表示
301（Moved Permanently） - 资源的URI已被更新
303（See Other） - 其他（如，负载均衡）
304（not modified）- 资源未更改（缓存）
400 （bad request）- 指代坏请求（如，参数错误）
404 （not found）- 资源不存在
406 （not acceptable）- 服务端不支持所需表示
500 （internal server error）- 通用错误响应
503 （Service Unavailable）- 服务端当前无法处理请求

```

POST

```

不安全且不幂等
使用服务端管理的（自动产生）的实例号创建资源
创建子资源
部分更新资源
如果没有被修改，则不过更新资源（乐观锁）
200（OK）- 如果现有资源已被更改
201（created）- 如果新资源被创建
202（accepted）- 已接受处理请求但尚未完成（异步处理）
301（Moved Permanently）- 资源的URI被更新
303（See Other）- 其他（如，负载均衡）
400（bad request）- 指代坏请求
404 （not found）- 资源不存在
406 （not acceptable）- 服务端不支持所需表示
409 （conflict）- 通用冲突
412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
415 （unsupported media type）- 接受到的表示不受支持
500 （internal server error）- 通用错误响应
503 （Service Unavailable）- 服务当前无法处理请求
```

### 4.2、编写接口代码

#### 根模块下的urls.py中声明（traveling_django/urls.py）

```
urlpatterns = [
    ...
    # 系统模块
    path('system/', include('system.urls'))
]
```

#### 编写视图函数

```
from django import http
from django.http import HttpResponse
from django.shortcuts import render
from system.models import Slider


def slider_list(request):
    """
    轮播图接口
    [
        "meta": {}
        "objects": []
    ]
    """
    data = {
        'meta': {

        },
        'objects': []
    }
    queryset = Slider.objects.filter(is_valid=True)
    for item in queryset:
        data['objects'].append({
            'id': item.id,
            'img_url': item.img.url,
            'target_url': item.target_url,
            'name': item.name
        })

    return http.JsonResponse(data)

```

#### 分配url地址（system/urls.py）

```
from django.urls import path

from system import views

urlpatterns = [
    path('slider/list/', views.slider_list, name='slider+list'),

]
```

### 4.3、模拟HTTP请求，测试验证接口
