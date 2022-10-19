---
title: 1.134-sight/detail模块(景点详细信息接口开发)
date: 2022-09-19 18:00:00 +0800
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

### 接口实现

#### 设计接口返回标准

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

#### 分配url地址（sight/urls.py）

```
from django.urls import path

from sight import views

urlpatterns = [
    ...
    # 景点详情接口
    path('sight/detail/<int:pk>/', views.SightDetailView.as_view(), name='sight_detail'),
]
```

#### 编写视图函数

```
class SightDetailView(DetailView):
    """ 景点详细信息 """
    def get_queryset(self):
        return Sight.objects.all()

    def render_to_response(self, context, **response_kwargs):
        page_obj = context['object']
        if page_obj is not None:
            if page_obj.is_valid:
                return NotFoundJsonResponse()
            data = {}  # TODO
            return http.JsonResponse(data)
        else:
            return NotFoundJsonResponse()
```

#### 重构响应对象

```
class SightDetailSerializer(serializers.BaseSerializer):
    """ 景点详情的对象 """

    def to_dict(self):
        obj = self.obj
        return {
            'id': obj.id,
            'name': obj.name,
            'desc': obj.desc,
            'img_img': obj.banner_img.url,
            'content':obj.content,
            'score': obj.score,
            'province': obj.province,
            'min_price': obj.min_price,
            'city': obj.city,
            'area': obj.area,
            'town': obj.town,
            # TODO
            'comment_count': 0
        }
```

改写视图函数

```
class SightDetailView(DetailView):
    """ 景点详细信息 """
    def get_queryset(self):
        return Sight.objects.all()

    def render_to_response(self, context, **response_kwargs):
        page_obj = context['object']
        if page_obj is not None:
            if page_obj.is_valid:
                return NotFoundJsonResponse()
            data = serializers.SightDetailSerializer(page_obj).to_dict()
            return http.JsonResponse(data)
        else:
            return NotFoundJsonResponse()
```

### 4.3、模拟HTTP请求，测试验证接口
