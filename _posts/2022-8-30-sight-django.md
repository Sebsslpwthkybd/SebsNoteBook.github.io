---
title: 1.122-sight模块(景点列表接口开发)
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

# 后端

## sight模块

### 1、创建sight模块

```
python manage.py startapp sight
```

### 2、添加进INSTALL_APPS(traveling_django/settings.py)

```
INSTALLED_APPS = [
	...
    'sight.apps.SightConfig',
]

```

### 3、ORM模型

#### ORM模型设计（sight/models.py）

景点模型

```
from django.db import models


# Create your models here.
class Sight(models.Model):
    """ 景点基础信息 """

    name = models.CharField('名称', max_length=64)
    desc = models.CharField('描述', max_length=256)
    main_img = models.ImageField('主图', upload_to='%Y%m/sight/', max_length=256)
    banner_img = models.ImageField('详情主图', upload_to='%Y%m/sight/', max_length=256)
    content = models.TextField('详细')
    score = models.FloatField('评分', default=5)
    min_price = models.FloatField('最低价格', default=0)
    province = models.CharField('省份', max_length=32)
    city = models.CharField('市区', max_length=32)
    area = models.CharField('区/县', max_length=32, null=True)
    town = models.CharField('乡镇', max_length=32, null=True)

    is_top = models.BooleanField('是否为精选景点', default=False)
    is_hot = models.BooleanField('是否为热门景点', default=False)

    is_valid = models.BooleanField('是否有效', default=True)
    created_at = models.DateTimeField('创建时间', auto_now_add=True)
    updated_at = models.DateTimeField('修改时间', auto_now=True)

    class Meta:
        db_table = 'sight'
        ordering = ['-updated_at']
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
    # 景点模块
    path('sight/', include('sight.urls')),
]
```

#### 分配url地址（sight/urls.py）

```
from django.urls import path

from sight import views

urlpatterns = [
    # 景点列表接口
    path('sight/list/', views.SightListView.as_view(), name='sight_list'),

]
```

#### 编写视图函数（需要分页）

##### 使用分页模型

```
class SightListView(ListView):
    """ 景点列表 """

    paginate_by = 5  # 分页，每页放五条数据
```

##### 重写查询方法（需要实现查询热门景点、精选经典和按景点名称查询）

```
    def get_queryset(self):
        """ 重写查询方法 """
        query = Q(is_valid=True)
        # 1. 热门景点
        is_hot = self.request.Get.get('is_hot', None)
        if is_hot:
            query = query & Q(is_hot=True)

        # 2. 精选景点
        is_top = self.request.Get.get('is_top', None)
        if is_top:
            query = query & Q(is_top=True)

        # TODO 3. 景点名称搜索

        query_set = Sight.objects.filter(query)
        return query_set
```

##### 重写返回方法

```
    def render_to_response(self, context, **response_kwargs):
```

###### 获得数据包

```
        page_obj = context['page_obj']
```

设定返回结构

```
        data = {
            'meta': {
                'total_count': page_obj.paginator.count,
                'page_count': page_obj.paginator.num_pages,
                'current_page': page_obj.number
            },
            # 'objects': page_obj.object_list -> 不能直接序列化为json对象
            'objects': []
        }
```

手动序列化objects里要携带的数据

```
        for item in page_obj.object_list:
            data['objects'].append({
                'id': item.id,
                'name': item.name,
                'main_img': item.main_img.url,
                'score': item.score,
                'province': item.province,
                'min_price': item.min_price,
                'city': item.city,
                # TODO
                'comment_count': 0
            })
```

将返回类型设定为JSON对象

```
        return http.JsonResponse(data)
```

### 4.3、模拟HTTP请求，测试验证接口
