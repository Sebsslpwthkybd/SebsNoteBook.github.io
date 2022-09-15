---
title: 1.133-景点详情（ORM模型开发）
date: 2022-09-12 10:00:00 +0800
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

## account模块

### 1、创建account模块

```
python manage.py startapp account
```

### 2、添加进INSTALL_APPS(traveling_django/settings.py)

```
INSTALLED_APPS = [
	...
    # 账户模块
    'account.apps.AccountConfig',
]

```

### 3、ORM模型

#### ORM模型设计

##### 公共模型（utils/models.py）

```
from django.db import models


class CommonModel(models.Model):
    """ 模型公共类 """
    is_valid = models.BooleanField('是否有效', default=True)
    created_at = models.DateTimeField('创建时间', auto_now_add=True)
    updated_at = models.DateTimeField('修改时间', auto_now=True)

    class Meta:
        abstract = True  # 必须写，不然会生成数据库表

```

##### 用户账户模型（account/models.py）

```
from django.db import models
from utils.models import CommonModel


# Create your models here.
class User(CommonModel):
    """用户模型"""
    username = models.CharField('用户名', max_length=32, unique=True)
    password = models.CharField('密码', max_length=256)
    avatar = models.ImageField('用户头像', upload_to='avatar/%Y%m', null=True, blank=True)
    nickname = models.CharField('昵称', max_length=32, unique=True)

    class Meta:
        db_table = 'account_user'
 
```

###### 检查语法

```
python manage.py check
```

###### 生成同步原语

```
python manage.py makemigrations
```

###### 同步模型至数据库

```
 python manage.py migrate
```

##### 图片关联模型（system/models.py）

```
from account.models import User
from utils.models import CommonModel


class ImageRelated(CommonModel):
    """ 图片关联 """
    img = models.ImageField('图片', max_length=256, upload_to='%Y%m/file/')
    summary = models.CharField('图片说明', max_length=32, null=True, blank=True)
    user = models.ForeignKey(User,
                             related_name='upload_images',
                             verbose_name='上传的用户',
                             null=True,
                             on_delete=models.SET(None))
    content_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    object_id = models.IntegerField('关联的模型')
    content_object = GenericForeignKey('content_type', 'object_id')

    class Meta:
        db_table = 'system_image_related'

```

##### 轮播图模型修改（system/models.py）

```
class Slider(CommonModel):
    name = models.CharField('名称', max_length=32)
    desc = models.CharField('描述', max_length=100, null=True, blank=True)
    types = models.SmallIntegerField('展现的位置', default=10)
    img = models.ImageField('图片地址', max_length=255, upload_to='%Y%m/slider')
    reorder = models.SmallIntegerField('排序字段', default=0, help_text='数字越大越靠前')
    start_time = models.DateTimeField('生效开始时间', null=True, blank=True)
    end_time = models.DateTimeField('生效开始时间', null=True, blank=True)
    target_url = models.CharField('跳转的地址', max_length=100, null=True, blank=True)

    class Meta:
        db_table = 'system_slider'
        ordering = ['-reorder']
```

##### 景点基础信息修改（sight/models.py）

```
from system.models import ImageRelated
from utils.models import CommonModel


class Sight(CommonModel):
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

    images = GenericRelation(ImageRelated,
                             verbose_name='关联的图片',
                             related_name='rel_sight_images')

    class Meta:
        db_table = 'sight'
        ordering = ['-updated_at']
```

##### 景点详情模型（sight/models.py）

```
class Info(models.Model):
    """ 景点详情 """
    sight = models.OneToOneField(Sight, on_delete=models.CASCADE)
    entry_explain = models.CharField('入园参考', max_length=1024, null=True, blank=True)
    play_way = models.TextField('特色玩法', null=True, blank=True)
    tips = models.TextField('温馨提示', null=True, blank=True)
    traffic = models.TextField('交通到达', null=True, blank=True)

    class Meta:
        db_table = 'sight_info'
```

##### 景点门票模型（sight/models.py）

```
class Ticket(CommonModel):
    """ 门票 """
    sight = models.ForeignKey(Sight, related_name='tickets',
                              verbose_name='景点门票',
                              on_delete=models.PROTECT)
    name = models.CharField('名称', max_length=128)
    desc = models.CharField('描述', max_length=64, null=True, blank=True)
    types = models.SmallIntegerField('类型',
                                     choices=TicketTypes.choices,
                                     default=TicketTypes.ADULT,
                                     help_text='默认为成人票')
    price = models.FloatField('价格（原价）')
    discount = models.FloatField('折扣', default=10)
    total_stock = models.PositiveIntegerField('总库存', default=0)
    remain_stock = models.PositiveIntegerField('剩余库存', default=0)
    expire_date = models.IntegerField('有效期', default=1)
    return_policy = models.CharField('退改政策', max_length=64, default='条件退')
    has_invoice = models.BooleanField('是否提供发票', default=True)
    entry_way = models.SmallIntegerField('入园方式',
                                         choices=EntryWay.choices,
                                         default=EntryWay.BY_TICKET,)
    tips = models.TextField('预定须知', null=True, blank=True)
    remark = models.TextField('其他说明', null=True, blank=True)
    status = models.SmallIntegerField('状态',
                                      choices=TicketStatus.choices,
                                      default=TicketStatus.OPEN,)

    class Meta:
        db_table = 'sight_ticket'
```

##### 评论及回复模型（sight/models.py）

```
class Comment(CommonModel):
    """ 评论及回复 """
    user = models.ForeignKey(User, verbose_name='评论人',
                             related_name='comments',
                             on_delete=models.CASCADE)
    sight = models.ForeignKey(Sight, verbose_name='景点',
                              related_name='comments',
                              on_delete=models.CASCADE)
    content = models.TextField('评论内容', blank=True, null=True)
    is_top = models.BooleanField('是否置顶', default=False)
    love_count = models.IntegerField('点赞次数', default=0)
    score = models.FloatField('评分', default=5)

    ip_address = models.CharField('IP地址', blank=True, null=True, max_length=64)
    is_public = models.SmallIntegerField('是否公开', default=1)
    reply = models.ForeignKey('self',
                              blank=True, null=True,
                              related_name='reply_comment',
                              verbose_name='回复',
                              on_delete=models.CASCADE)

    images = GenericRelation(ImageRelated,
                             verbose_name='关联的图片',
                             related_query_name='rel_comment_images')

    class Meta:
        db_table = 'sight_comment'
        ordering = ['-love_count', '-created_at']
```

###### 检查语法

```
python manage.py check
```

###### 生成同步原语

```
python manage.py makemigrations
```

###### 同步模型至数据库

```
 python manage.py migrate
```

##### 
