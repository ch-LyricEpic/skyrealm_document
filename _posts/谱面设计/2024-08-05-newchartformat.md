---
title: 谱面格式
date: 2024-08-05 20:00:00 +0800
author: SkyRealm
categories: [谱面设计]
pin: false
math: true
mermaid: true
---
# 谱面格式

本篇文档将详细记录SkyRealm的谱面基本格式。

## 前言

### Time

time的格式为`[beat, nume, deno]`，其中beat为小节，nume为分子，deno为分母，可理解为在第**beat**拍的**nume**/**deno**处。

### 缓动列表
（之后再写）

## 格式

> 点击**类型**一栏以获得更多资讯。
{: .prompt-info }

### chart.json
| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| chartInfo | 谱面基本信息 | [`chartInfo`](#chartInfo) |
| bpmList | 谱面bpm列表 | [`List[bpm]`](#bpm) |
| lines | 谱面判定线列表 | [`List[line]`](#line) |

### chartInfo
| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| song_id | 歌曲唯一ID | String |
| offset | 谱面音频偏移(ms) | Float |
| difficulty | 谱面难度等级 (0/1/2/3) | Int |

### bpm
| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| bpm | 新bpm | Float |
| time | 时间 | [`Time`](#time) |

### line
| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| tracks | 判定轨道 | [`List[track]`](#track) |
| segmentEvents | 判定线函数取样点数目事件 | [`List[genericEvent]`](#genericEvent) |
| rotateEvents | 判定线旋转事件 | [`List[genericEvent]`](#genericEvent) |
| fadeEvents | 判定线透明度事件 | [`List[genericEvent]`](#genericEvent) |
| offsetEvents | 判定线偏移事件 | [`List[offsetEvent]`](#offsetEvent) |
| functionEvents | 判定线函数事件 | [`List[functionEvent]`](#functionEvent) |

### track
| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| notes | notes列表 | [`List[Note]`](#note) |
| slideEvents | 轨道偏移事件 | [`List[genericEvent]`](#genericEvent) |
| fadeEvents | 轨道透明度事件 | [`List[genericEvent]`](#genericEvent) |
| speedEvents | 轨道速度事件 | [`List[genericEvent]`](#genericEvent) |

### note

只有当 `type` 为2时，`end` 才有效。
| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| type | 0为tap, 1为drag, 2为hold, 3为beat | Int |
| start | 击打时间 | [`Time`](#time) |
| end | hold结束时间 | [`Time`](#time) |
| speed | note速度 | Float |
| sp | 是否为fake note | Boolean |
| events | note事件 | [`List[noteEvent]`](#noteEvent) |

### noteEvent
目前`type`可为:
- **changeTrack**: 改变判定轨道。

| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| type | 事件类型 | String |
| start | 开始时间 | [`Time`](#time) |
| end | 结束时间 | [`Time`](#time) |
| ease | 缓动 | Int |
| from | 数值开始 | Float |
| to | 数值结束 | Float |

### genericEvent
**genericEvent**分为两种，一种为瞬时设定，另一种为动画效果设定。

| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| time | 事件时间 | [`Time`](#time) |
| value | 事件数值 | Float |

或

| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| start | 开始时间 | [`Time`](#time) |
| end | 结束时间 | [`Time`](#time) |
| ease | 缓动 | Int |
| from | 数值开始 | Float |
| to | 数值结束 | Float |

### offsetEvent
| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| start | 开始时间 | [`Time`](#time) |
| end | 结束时间 | [`Time`](#time) |
| ease | 缓动 | Int |
| fromX | X开始 | Float |
| toX | X结束 | Float |
| fromY | Y开始 | Float |
| toY | Y结束 | Float |

### functionEvent
| 参数名 | 描述 | 类型 |
| :- | :- | -: |
| start | 开始时间 | [`Time`](#time) |
| end | 结束时间 | [`Time`](#time) |
| value | 函数 | [`function`](#function) |

### function
此具体格式为AST抽象语法树。每个`function`都有其`type`，可为以下的其中一种：
- add
- sub
- mul
- div
- sin
- pow

`function`也同样有其`left`数值及`right`数值，可为`function`或者一个常数。(或者`value`，当`type`的参数数目为1时)

要表达 **y = x^2 + 3**:
```json
{
  "type": "add",
  "left": {
    "type": "pow",
    "left": "x",
    "right": 2,
  },
  "right": 3
}
```

**y = sin(x + time)**:
```json
{
  "type": "sin",
  "value": {
    "type": "add",
    "left": "x",
    "right": "time"
  }
}
```
## 范例

以下是一张谱面的范本。

```json
{
  "chartInfo": {
    "song_id": "SkyRealm",
    "offset": 100,
    "difficulty": 0
  },
  "bpmList": [
    {
      "bpm": 120,
      "time": [1, 1, 4]
    },
    {
      "bpm": 150,
      "time": [2, 1, 4]
    }
  ],
  "lines": [
    {
      "tracks": [
        {
          "notes": [
            {"type": 0, "start": [0, 0, 1], "speed": 1, "sp": false},
            {"type": 1, "start": [1, 1, 4], "speed": 1, "sp": true},
            {"type": 2, "start": [3, 1, 2], "end": [5, 1, 2], "speed": 1, "sp": false},
            {"type": 0, "start": [5, 0, 1], "speed": 1, "sp": false, "events": [
              {
                "type": "changeTrack",
                "from": 0,
                "to": 1,
                "start": [4, 0, 1],
                "end": [4, 1, 2],
                "ease": 0
              }
            ]}
          ],
          "slideEvents": [
            {
              "time": [0, 0, 1],
              "value": 0.3
            }
          ],
          "fadeEvents": [
            {
              "time": [0, 0, 1],
              "value": 1
            }
          ],
          "speedEvents": [
            {
              "time": [0, 0, 1],
              "value": 1
            }
          ]
        },
        {
          "notes": [],
          "slideEvents": [
            {
              "time": [0, 0, 1],
              "value": 0.5
            },
            {
              "start": [1, 0, 1],
              "end": [2, 0, 1],
              "from": 0.5,
              "to": 0.7,
              "ease": 0
            }
          ],
          "fadeEvents": [
            {
              "time": [0, 0, 1],
              "value": 1
            },
            {
              "start": [5, 0, 1],
              "end": [6, 0, 1],
              "from": 1,
              "to": 0,
              "ease": 0
            }
          ],
          "speedEvents": [
            {
              "time": [0, 0, 1],
              "value": 1
            }
          ]
        }
      ],
      "segmentEvents": [
        {
          "time": [0, 0, 1],
          "value": 100
        }
      ],
      "rotateEvents": [
        {
          "start": [0, 0, 1],
          "end": [2000, 0, 1],
          "value": 0
        }
      ],
      "fadeEvents": [
        {
          "start": [0, 0, 1],
          "end": [200, 0, 1],
          "value": 255
        }
      ],
      "offsetEvents": [
        {
          "start": [0, 0, 1],
          "end": [10000, 0, 1],
          "fromX": 0,
          "toX": 0.1,
          "fromY": 0.5,
          "toY": 0.9
        }
      ],
      "functionEvents": [
        {
          "start": [0, 0, 1],
          "end": [2, 0, 1],
          "value": 0
        },
        {
          "start": [2, 0, 1],
          "end": [1000, 0, 1],
          "value": {
            "type": "add",
            "left": {
              "type": "mul",
              "left": "x",
              "right": "x"
            },
            "right": "time"
          }
        }
      ]
    }
  ]
}
```