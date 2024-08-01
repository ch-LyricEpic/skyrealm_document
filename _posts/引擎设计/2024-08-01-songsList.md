---
title: songsList格式
date: 2024-08-01 15:00:00 +0800
author: SkyRealm
categories: [引擎设计]
pin: false
math: true
mermaid: true
---

本篇文档将详细记录SkyRealm的歌曲选择列表 **songsList.json** 的基本格式。  

## 格式
### songsList.json

| 参数名     | 参数描述 | 参数类型       |  备注 |
| :------ | :--- | :---------: | --: |
| version | 版本   | String     |     |
| songs   | 歌曲列表 | list[song] |     |

### song

| 参数名          | 参数描述  | 参数类型       |                                       备注 |
| :----------- | :-----: | :----------: | ---------------------------------------: |
| song_id      | 歌曲ID  | String     | 必须为唯一 (primary key)，一般为`artist.songName` |
| chapter      | 章节    | String     |                                   歌曲所属章节 |
| name         | 曲名    | String     |                                          |
| bpm          | 歌曲bpm | String     |     只用于在游戏中展示（如bpm为300至305，可填 `300-305`） |
| artist       | 曲师    | String     |                                          |
| illustration | 曲绘路径  | String     |                                     档案路径 |
| illustrator  | 绘师    | String     |                                          |
| audio        | 歌曲路径  | String     |                                     档案路径 |
| charter      | 谱师    | String     |                                          |
| difficulties | 难度    | list[diff] |                                 一般列表长度为3 |

### diff

> 请注意下列带有"可选"的参数可用于*覆盖* **song** 的数值。如未指定，则系统会选用 **song** 本身的数值。
{: .prompt-info }

| 参数名          | 参数描述  | 参数类型   |               备注 |
| :----------- | :------: | :-------: | ---------------: |
| type         | 谱面难度  | Int    |          0/1/2/3 |
| rating       | 谱面等级  | Int    |           歌曲显示难度 |
| charter      | 谱师    | String |               可选 |
| bpm          | 歌曲bpm | String | 同**song**的bpm，可选 |
| artist       | 曲师    | String |               可选 |
| name         | 曲名    | String |               可选 |
| illustration | 曲绘路径  | String |          档案路径，可选 |
| illustrator  | 绘师    | String |               可选 |
| audio        | 歌曲路径  | String |          档案路径，可选 |

## 范例


对于一首普通歌曲（以Arcaea的reinvent为例），**song**如下：
```json
{
  "song_id": "SoundSouler.reinvent",
  "chapter": "freepack",
  "name": "Reinvent",
  "bpm": "174",
  "artist": "SoundSouler",
  "illustration": "reinvent.png",
  "illustrator": "Khronetic",
  "audio": "reinvent.wav",
  "charter": "k//eternal",
  "difficulties": [
    {
      "type": 0,
      "rating": 2
    },
    {
      "type": 1,
      "rating": 6
    },
    {
      "type": 2,
      "rating": 8,
      "charter": "k//eternal + nitro"
    }
  ]
}
```

此时，`难度1`的rating为`2`，谱师为 `k//eternal`；

`难度2`的rating为`6`，谱师为 `k//eternal`；

`难度3`的rating为`8`，谱师为 `k//eternal + nitro`。

---

对于特别歌曲（以Arcaea的Last为例），**song**如下：
```json
{
  "song_id": "onoken.last",
  "chapter": "slientAnswer",
  "name": "Last",
  "bpm": "175",
  "artist": "onoken",
  "illustration": "last.png",
  "illustrator": "シエラ",
  "audio": "last.wav",
  "charter": "Arcaea",
  "difficulties": [
    {
      "type": 0,
      "rating": 4
    },
    {
      "type": 1,
      "rating": 7
    },
    {
      "type": 2,
      "rating": 9
    },
    {
      "type": 3,
      "rating": 9,
      "name": "Last | Moment",
      "illustration": "last_moment.png",
      "audio": "last_moment.wav"
    }
  ]
}
```

此时，`难度1`的rating为`4`;

`难度2`的rating为`7`;

`难度3`的rating为`9`;

`难度4`的rating为`9`，曲名变为`Last | Moment`，曲绘及音频也有所改变。
