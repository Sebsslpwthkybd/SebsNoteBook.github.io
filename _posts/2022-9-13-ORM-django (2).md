---
title: 1.133-景点详情（重构响应对象）
date: 2022-09-13 12:00:00 +0800
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

# 基类设定

## 响应列表对象的基类（utils/serializers.py）

### 分页元数据

```
class MetaSerializer(object):
    """ 分页元数据 """
    def __init__(self, page, page_count, total_count, **kwargs):
        """
        :param page: 当前第几页
        :param page_count: 总页数
        :param total_count: 总记录数
        """
        self.page = page
        self.page_count = page_count
        self.total_count = total_count

    def to_dict(self):
        return {
            'total_count': self.total_count,
            'page_count': self.page_count,
            'current_page': self.page,
        }
```

### 分页封装（使用时需要重写get_obj方法）

```
class BaseListPageSerializer(object):
    """ 分页类封装"""
    def __init__(self, page_obj, paginator=None, object_list=[]):
        """
        :param page_obj: 当前页的对象
        :param paginator: 分页器的对象
        :param object_list: 当前页的数据列表
        """
        self.page_obj = page_obj
        self.paginator = paginator if paginator else page_obj.paginator
        self.object_list = object_list if object_list else page_obj.object_list

    def get_obj(self, obj):
        """ 对象的内容，子类重写"""
        return {}

    def to_dict(self):
        page = self.page_obj.number
        page_count = self.paginator.num_pages
        total_count = self.paginator.count
        meta = MetaSerializer(page=page, page_count=page_count, total_count=total_count).to_dict()

        objects = []
        for obj in self.object_list:
            objects.append(self.get_obj(obj))

        return {
            'meta': meta,
            'objects': objects,
        }
```

## 响应错误的基类（utils/response.py）

```
from django.http import JsonResponse


class NotFoundJsonResponse(JsonResponse):
    """ 400 对应Json响应"""
    status_code = 400
    
    def __init__(self, *args, **kwargs):
        data = {
            "error_code": "4040000",
            "error_msg": "您访问的内容不存在或已被删除",
        }
        super().__init__(data, *args, **kwargs)
        
```

## 应用

### 景点列表的对象（sight/serializers.py）

```
from utils import serializers


class SightListSerializer(serializers.BaseListPageSerializer):
    """ 景点列表的对象 """

    def get_obj(self, obj):  # 重写以返回自定义的字段
        return {
            'id': obj.id,
            'name': obj.name,
            'main_img': obj.main_img.url,
            'score': obj.score,
            'province': obj.province,
            'min_price': obj.min_price,
            'city': obj.city,
            # TODO
            'comment_count': 0
        }
```

### 修改返回方法（sight/views.py）

```
from sight import serializers
from utils.response import NotFoundJsonResponse
    
    
    def render_to_response(self, context, **response_kwargs):
        page_obj = context['page_obj']  # 打断点+调试模式可以知道这里面包含什么数据
        if page_obj is not None:
            data = serializers.SightListSerializer(page_obj).to_dict()
            return http.JsonResponse(data)
        else:
            return NotFoundJsonResponse()
```

