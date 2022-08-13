---
title: web端展示查询的数据（html模板）
date: 2022-08-01 18:00:00 +0800
categories: [SogouTop50]
tags: [hadoop]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

#### 导入包

##### 1、将js源码文件放入static/js中

##### 2、模板中引入

```
<head>
    <meta charset="utf-8" />
    <script src="{{url_for('static', filename='jquery-3.6.0.min.js')}}"></script>
    <script src="{{url_for('static', filename='echarts.js')}}"></script>
</head>
```

#### 图表

```
<body>
```

##### 定义DOM的宽高

```
<div id="main" style="width: 1820px;height:980px;"></div>
    <script type="text/javascript">
```

##### 异步请求

```
$.ajax({  // 需要包含整个使用数据的过程
        url: 'http://127.0.0.1:5000/queryJson',   // 一定要写全（包括‘http://’）
        success: function (data) {
          data = JSON.parse(data);
          console.log(data);
```

##### 指定图标的配置项和数据

```
	var option = {
        title: {  // 标题
          text: 'Sogou_top50'
        },
        tooltip: {},
        xAxis: {  // x轴
          data: data['keyword']  // 由访问返回json的url得到的data字典里的keyword这个key所对应的value
        },
        yAxis: {  // 示例名
        },
        series: [  // y轴
          {
            name: '点击数',
            type: 'bar',
            data: data['count']
          }
        ]
      }
          // 使用刚指定的配置项和数据显示图表。
        myChart.setOption(option);
      }  // 配置项结束
      });  // 异步请求的结束
	</script>
</body>
```

