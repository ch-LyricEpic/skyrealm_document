---
title: 谱面基本格式
date: 2024-07-30 20:00:00 +0800
author: SkyRealm
categories: [谱面设计]
pin: false
math: true
mermaid: true
---

本篇文档将详细记录SkyRealm的谱面基本格式。  
以下是一张示例谱面。本文将基于示例谱面进行解析。
```
displayInfo:{'song_id':'SkyRealm'}
globalInfo:{'difficulty':1, 'rate':10.4}
chartInfo:{'maxtrack':'4', 'offset':'100'}
bpmList:[(120.0,-1),(125.0,10.12345),(130.0,20.54321)]
speedList:[(1.4,-1),(3,10.12345),(1.7,20.54321)]
note:{
    {'type':'tap', 'track':1, 'start':[1,1,4], 'speed':1.0, 'isSpNote':'True'}
    {'type':'drag', 'track':2, 'start':[1,2,4], 'speed':1.0, 'isSpNote':'False'}
    {'type':'hold', 'track':3, 'start':[1,2,4], 'end':[2,2,4], 'speed':1.0, 'isSpNote':'False'}
}
event:{
    {'object':'note', 'id':1, 'func':'changetrack(0.5,4,1)'}
}
```

**一张SkyRealm谱面一般由几个元素组成，元素又可以由子元素组成。元素均有属性。**  
SkyRealm谱面最根节的元素称为父元素。以下是SkyRealm常有的父元素列表，同时我们会详细解释这些父元素。  

## 父元素

### **displayInfo**
纪录songsList.json下當前的**song**元素。请注意该数值为动态加载，并不实际存在于谱面文件中。 

### **globalInfo**
记录谱面的全局信息。
一般情况下globalInfo包含以下参数：  

| 参数名/ValueName | 参数描述/ValueDescription | 参数类型 |
| :--------------------------- | :--------------- | ------: |
| difficulty | 当前谱面在当前歌曲下的难度等级(0/1/2/3) | Int |
| rate | 当前谱面**实际**定级 | Float |

在实际计算中请使用`globalInfo.rate`，而其他情况则可使用`displayInfo`。 

### **chartInfo**
记录谱面的游玩优化信息。这些信息仅会在游戏开始时读取。  
一般情况下chartInfo包含以下参数：  

| 参数名/ValueName | 参数描述/ValueDescription | 参数类型 |
| :--------------------------- | :--------------- | ------: |
| maxtrack | 本谱面最大轨道数量 | Int |
| offset | 记录谱面延迟 | Float |

### **bpmList**
记录谱面的 BPM 值。该元素会在游戏开始时被读取。  
bpmList 元素记录格式为 `[(curbpm1, time1),(curbpm2, time2)]`。  
其中，bpmList元素中第一项数据`(curbpm1,time1)`为谱面初始BPM值，也就是在刚刚进入游戏时的设定BPM值。第一项数据的`time`值为`-1`。**如果该歌曲全曲BPM没有变化，则bpmList有且仅有这一项初始数据。**  
如果该歌曲BPM有变化，则`(curbpm,time)`中`curbpm`对应目标BPM值，`time`对应变化时歌曲已播放的时间，单位为秒。  

### **speedList**
记录谱面全局流速。该元素会在游戏开始时被读取。  
该元素记录格式与bpmList元素相同，在此不再过多赘述。speedList作用效果将会在note元素中详细说明。  

### **note**
该父元素含有的**每一个子元素都代表一个真音符 Note。**真音符即为可以被打击并计分的音符。  
note父元素的子元素，一般格式如下：
```
{'type':'tap', 'track':1, 'start':[1,1,4], 'speed':'1.0', 'isSpNote':'True'}
```
一般情况下note的所有子元素(音符 Note)包含以下参数：  

| 参数名/ValueName | 参数描述/ValueDescription | 参数类型 |
| :--------------------------- | :--------------- | ------: |
| type | 描述该音符类型。一般为(tap/hold/drag/beat) | String |
| track | 记录该音符的初始生成轨道(1/2/3/4/...) | Int |
| start | 记录该音符的打击时间 | List |
| speed | 该音符的私有速度作用值 | Float |
| end | 记录该音符的结束时间 | List |
| isSpNote | 是否为特殊音符 | Bool |

其中，`type`参数中`tap`对应点按音符，`hold`对应长键，`drag`对应装饰键，`beat`对应拍键。  
`end`参数仅有`hold`音符有。    
游戏会根据Note各参数自动推断出Note从生成到打击开始的时间值，我们规定：**对于所有音符，Note从生成到打击开始为Note的 【流程】** ，一般来说流程结束瞬间此Note位于Early/Late判定临界。而对于Hold音符，**在Note流程结束后，它还存在一段end时间**，需要注意。  
**流程时长记为单位1，这个属性将会在接下来的event说明中用到。**  

Note的`start`和`end`是指开始/结束打击。这两个参数格式是一样的,都是`[beat, nume, deno]`。`dume`全称Numerator(分子)，`deno`全称Denominator(分母)。对于start参数，可理解为:  
**在本曲的第 beat 拍的 nume/deno 处打击该Note。**  
则：对于示例中`[1,1,4]`，意为：**在本曲的第1拍第1/4处打击**。  

Note中`speed`参数为该音符的私有速度作用值。令Note的实际速度为`Vnr`，私有速度作用值为`Vns`，全局速度为`Vg`。则：  

$$
Vnr = Vg \times Vns
$$

`isSpNote`是声明该Note是否为特殊音符的参数。

### **event**
event是谱面的事件系统，主要作用于谱面的表演等附加内容。event的具体格式，将会在之后说明。