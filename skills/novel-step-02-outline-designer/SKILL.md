---
name: novel-step-02-outline-designer
description: 大纲师 - 设计 20 章完整大纲，分配核心冲突三要素
---

# 大纲师

## 职责

根据选题师的输出，设计 20 章完整大纲，按照节奏铁律模板分配核心冲突三要素。

## 输入格式

```json
{
  "category": "女频-追妻火葬场",
  "novel_title": "ICU 门外，他抱白月光笑时，我重生了",
  "estimated_words": 18000,
  "core_climax_points": [
    "前期：深情女主 vs 冷漠渣男 + 白月光陷害",
    "中期：心死女主 vs 幡然醒悟疯狂追妻的前夫",
    "后期：女主复仇搞事业 vs 渣男阻挠 + 后悔"
  ],
  "characters": {
    "protagonist": {
      "name": "江念",
      "gender": "女",
      "role": "女主（重生/觉醒）",
      "alignment": "protagonist",
      "personality": {
        "start": "深情付出",
        "middle": "心死觉醒",
        "end": "独立女王"
      }
    },
    "antagonist_main": {
      "name": "顾辞",
      "gender": "男",
      "role": "渣男前夫",
      "alignment": "antagonist",
      "personality": {
        "start": "冷漠轻视",
        "middle": "幡然醒悟",
        "end": "后悔追妻"
      }
    },
    "antagonist_secondary": {
      "name": "沈离",
      "gender": "女",
      "role": "白月光/心机女",
      "alignment": "antagonist",
      "relationship_to_protagonist": "情敌",
      "personality": {
        "start": "表面柔弱",
        "middle": "心机深沉",
        "end": "被打脸"
      }
    },
    "secondary_characters": [
      {
        "name": "江父",
        "gender": "男",
        "role": "女主父亲",
        "alignment": "neutral",
        "relationship_to_protagonist": "父女"
      },
      {
        "name": "江母",
        "gender": "女",
        "role": "女主母亲",
        "alignment": "neutral",
        "relationship_to_protagonist": "母女"
      },
      {
        "name": "林悦",
        "gender": "女",
        "role": "闺蜜",
        "alignment": "ally",
        "relationship_to_protagonist": "好友"
      }
    ]
  },
  "tone": "虐恋爽感",
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "../outputs/[书名]/",
  "reference_path": "../docs/参考资料.md"
}
```

## 输出格式

```json
{
  "total_chapters": 20,
  "total_words": 18000,
  "chapters": [
    {
      "chapter_number": 1,
      "title": "ICU 门外",
      "summary": "女主林婉在 ICU 门外等陆沉，他却抱着白月光苏月出现，林婉心死跳楼重生",
      "word_count_target": 800,
      "conflict_stage": "前期压迫",
      "key_plot_points": [
        "ICU 门外等待",
        "陆沉抱白月光出现",
        "林婉心死跳楼",
        "重生回到三年前"
      ],
      "ending_hook_type": "悬念",
      "ending_hook": "睁开眼，发现自己回到了三年前的婚礼前夕"
    },
    {
      "chapter_number": 2,
      "title": "婚礼前夕",
      "summary": "林婉重生后决定取消婚礼，开始暗中收集证据",
      "word_count_target": 900,
      "conflict_stage": "前期压迫",
      "key_plot_points": [...],
      "ending_hook_type": "反转",
      "ending_hook": "陆沉突然出现：婉婉，明天就是我们的婚礼了"
    },
    ...
  ],
  "character_arc": {
    "江念": {
      "start": "深情付出",
      "middle": "心死觉醒",
      "end": "独立女王"
    },
    "顾辞": {
      "start": "冷漠轻视",
      "middle": "幡然醒悟",
      "end": "后悔追妻"
    },
    "沈离": {
      "start": "表面柔弱",
      "middle": "心机深沉",
      "end": "被打脸"
    }
  },
  "characters": {
    "protagonist": {
      "name": "江念",
      "gender": "女",
      "role": "女主（重生/觉醒）",
      "alignment": "protagonist",
      "personality": {
        "start": "深情付出",
        "middle": "心死觉醒",
        "end": "独立女王"
      }
    },
    "antagonist_main": {
      "name": "顾辞",
      "gender": "男",
      "role": "渣男前夫",
      "alignment": "antagonist",
      "personality": {
        "start": "冷漠轻视",
        "middle": "幡然醒悟",
        "end": "后悔追妻"
      }
    },
    "antagonist_secondary": {
      "name": "沈离",
      "gender": "女",
      "role": "白月光/心机女",
      "alignment": "antagonist",
      "relationship_to_protagonist": "情敌",
      "personality": {
        "start": "表面柔弱",
        "middle": "心机深沉",
        "end": "被打脸"
      }
    },
    "secondary_characters": [
      {
        "name": "江父",
        "gender": "男",
        "role": "女主父亲",
        "alignment": "neutral",
        "relationship_to_protagonist": "父女"
      },
      {
        "name": "江母",
        "gender": "女",
        "role": "女主母亲",
        "alignment": "neutral",
        "relationship_to_protagonist": "母女"
      },
      {
        "name": "林悦",
        "gender": "女",
        "role": "闺蜜",
        "alignment": "ally",
        "relationship_to_protagonist": "好友"
      }
    ]
  },
  "timeline": {
    "early_oppression": "第1-5章",
    "mid_reversal": "第6-15章",
    "late_climax": "第16-20章"
  },
  "content_type": "outline",
  "session_id": "uuid-v4",
  "creation_timestamp": "2026x03-20T10:30:00Z",
  "work_dir": "../outputs/[书名]/",
  "reference_path": "../docs/参考资料.md"
}
```

## 节奏铁律模板

### 第 1 章（开篇冲突）
- **要求**：300 字内出冲突/背叛/死亡/觉醒
- **字数**：800-900字
- **钩子**：强悬念/反转

### 第 2-5 章（小爽点不断）
- **要求**：初步打脸、小爽点密集
- **字数**：800-900字/章
- **钩子**：悬念/危机/反转

### 第 6-15 章（升级打脸）
- **要求**：剧情反转、感情升温、升级打脸
- **字数**：900-1000字/章
- **钩子**：强钩子（每章必备）

### 第 16-19 章（高潮决战）
- **要求**：终极打脸、高潮对决
- **字数**：900-1000字/章
- **钩子**：最强钩子

### 第 20 章（完美结局）
- **要求**：完美结局、爽点拉满、逻辑闭环
- **字数**：1000字
- **钩子**：无（结局）

## 钩子类型

| 类型 | 说明 | 示例 |
|------|------|------|
| 悬念 | 留悬念引人猜测 | "她手里拿着一份文件，陆沉脸色瞬间变了" |
| 反转 | 意外转折 | "原来他不是出轨，而是..."去调查她的仇人" |
| 危机 | 突发危机 | "手术室外，医生缓缓摇头" |
| 打脸 | 爽点打脸 | "陆沉跪在雨中：婉婉，求你回来" |

## 人物弧光

### 女频追妻火葬场
| 人物 | 前期 | 中期 | 后期 |
|------|------|------|------|
| 女主 | 深情/付出 | 心死/觉醒 | 独立/复仇 |
| 男主 | 冷漠/轻视 | 醒悟/痛苦 | 悔恨/追妻 |

### 男频赘婿逆袭
| 人物 | 前期 | 中期 | 后期 |
|------|------|------|------|
| 男主 | 隐忍/扮猪 | 摊牌/打脸 | 巅峰/无敌 |
| 岳母 | 看不起/羞辱 | 震惊/后悔 | 跪舔 |

## 实现步骤

1. **分析选题信息**
   - 提取题材、基调、核心冲突
   - 确定人物弧光

2. **按节奏铁律分配章节**
   - 第 1 章：开篇冲突
   - 第 2-5 章：小爽点
   - 第 6-15 章：升级打脸
   - 第 16-19 章：高潮决战
   - 第 20 章：完美结局

3. **设计每章大纲**
   - 章节标题
   - 章节摘要
   - 关键情节点
   - 章末钩子

4. **生成 JSON 输出**
   - 20 章完整大纲
   - 人物弧光
   - 时间线分配

## 注意事项

1. 每章 800-1000 字，总字数 1.2万-1.8万
2. 开篇 300 字内必出冲突
3. 每章结尾必须有强钩子
4. 核心冲突三要素贯穿全文
5. 人物弧光完整、合理
