---
layout: post
title: iOS/Android实现商品sku弹窗并选择
date: 2019-5-20
tag: iOS技术
---

<h3>前言</h3>
现在做的是一个商城类型的app，然后里面有商品，有spu，有sku，然后就不可避免的出现了需要在spu大背景下选择不同的规格，属性得到目标的sku，然后添加到购物车中。因为需要原生的流程远高于web，所以这个需要开发iOS/Android两个版本。
最终开发的UI如下：
<img src="/images/iaskuimg.jpg" width="222px">

           
<h3>开发流程</h3>
1. h5页面把数据获取到，点添加购物车的时候把数据传给App；
2. App解析数据，并且得到目标数据结构；
3. 将规格，属性显示在页面上，然后客户选取得到目标sku。


<h3>具体流程</h3>
<h4>数据结构</h4>
数据经过和后端开发讨论了几版，最终确定如下：

```
{
    "id": 10086,
    "package_id": 128,
    "brand_name": "三全",
    "series_name": "千丝抓饼",
    "spu_name": "",
    "spu_alias": "",
    "type": 1,
    "sales_entity_id": 2,
    "default_item_id": "409",
    "default_item_type": "1",
    "standard": "320g/袋",
    "sku_info": [
      {
        "key": "口味",
        "values": [
          {
            "standard_value": "葱香味",
            "values": [
              409
            ]
          },
          {
            "standard_value": "辣酱",
            "values": [
              410
            ]
          },
          {
            "standard_value": "原味",
            "values": [
              411
            ]
          }
        ]
      }
    ],
    "items": {
      "409": [
        {
          "item_id": "409",
          "item_type": "1",
          "unit_name": "1袋装",
          "stock": 1084,
          "price": 700,
          "seller_id": "2"
        }
      ],
      "410": [
        {
          "item_id": "410",
          "item_type": "1",
          "unit_name": "1袋装",
          "stock": 1302,
          "price": 700,
          "seller_id": "2"
        }
      ],
      "411": [
        {
          "item_id": "411",
          "item_type": "1",
          "unit_name": "1袋装",
          "stock": 1095,
          "price": 700,
          "seller_id": "2"
        }
      ]
    },
    "image": {
      "409": "xxx409.jpg",
      "410": "xxx410.jpg",
      "411": "xxx411.jpg"
    },
    "name": {
      "409": "三全千丝抓饼320g葱香味",
      "410": "三全千丝抓饼320g辣酱",
      "411": "三全千丝抓饼320g原味"
    },
    "alias": {
      "409": "",
      "410": "",
      "411": ""
    }
  }

```

 数据结构确定了，然后进入开发阶段了。
 
<h3>Demo实现</h3>
地址：https://github.com/darling-shadow/AndroidDemo

OK，完成！

-------------------------------
本文由darling_shadow完成，谢谢。
 
 
