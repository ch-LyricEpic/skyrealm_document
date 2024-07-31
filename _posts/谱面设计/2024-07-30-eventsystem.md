---
title: 谱面事件系统
date: 2024-07-30 20:00:00 +0800
author: SkyRealm
categories: [谱面设计]
pin: false
math: true
mermaid: true
---

本篇文档将详细记录SkyRealm的谱面元素中`event`元素的使用方法。  
以下是示例event元素。本文将基于示例进行解析。
```
...
event:{
    {'object':'note', 'id':1, 'func':'changetrack(0.5,4,1)'}
}
...
```