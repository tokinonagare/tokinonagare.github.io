---
layout: 	  blog
title:		  合宿 API 文档说明
subtitle:     和后端交流 数据格式
categories:   React Javascript
tags: 		  React Javascript
redirect_from:
  - /web/heshu_api_doc.html
  - /2018/12/02/heshu_api_doc/
---

合宿报名
-----------

### 1. 提交合宿报名的申请表

#### 接口功能

> 向服务器发送合宿报名的申请

#### URL

> [？？？/application)

#### 支持格式

> JSON

#### HTTP请求方式

> POST

#### 提交参数

|参数|必选|类型|说明|
|:----- |:-------|:-----|----- |
|accommodationType |ture |string |合宿类型 |
|accommodationAddress |true |string |合宿地点 |
|houseType |true |string |房间类型 |
|startDate |true |string |合宿开始时间 |
|endDate |true |string |合宿结束时间 |
|extraDemand |option |string |额外需求 |

##### 示例

> 地址：[？？？/application](？？？)

```json
{
    "accommodationType":"捣蛋合宿",
    "address":"上海",
    "houseType":"双人房",
    "startDate":"2018-12-28",
    "endDate":"2018-12-29",
    "extraDemand":"API 格式说明"
}
```

#### 返回字段

|返回字段|字段类型|说明 |
|:----- |:------|:----------------------------- |
|status | boolean |返回申请是否成功。true：成功；false：失败。 |
|message | string | 返回的信息 |
|applicationNumber | int |如果成功，返回改次申请的订单号 |

##### 示例

```json
{
    "status": true,
    "message":"报名申请提交成功",
    "applicationNumber": 23223441
}
```