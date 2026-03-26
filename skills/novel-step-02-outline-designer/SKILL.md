---
name: novel-step-02-outline-designer
description: 大纲师 - 设计完整大纲，支持可变章节数和字数，分配核心冲突三要素
---

# 大纲师

## 职责

根据选题师的输出，设计完整大纲，按照节奏铁律模板分配核心冲突三要素。支持灵活的章节数和字数配置。

## 输入格式

```json
{
  "category": "女频-追妻火葬场",
  "novel_title": "ICU 门外，他抱白月光笑时，我重生了",
  "estimated_words": 18000,
  "target_chapters": 20,
  "chapter_word_min": 800,
  "chapter_word_max": 1000,
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
  "creation_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "./outputs/[书名]/work/",
  "reference_path": "./docs/参考资料.md"
```

**【关键】输出路径规则（所有技能必须统一）**：
- 工作目录：`./outputs/[书名]/work/`
- 参考资料：`./docs/参考资料.md`
- 输出目录：`./outputs/`
- 小说目录：`./outputs/[书名]/`
- 临时文件必须输出到 `work_dir`，禁止输出到 `outputs/` 根目录
}
```

### 输入参数说明

| 参数 | 类型 | 忌填 | 说明 | 默认值 |
|------|------|--------|------|--------|
| category | string | 否 | 题材分类 | - |
| novel_title | string | 否 | 小说标题 | - |
| estimated_words | number | 否 | 预估总字数 | - |
| target_chapters | number | 否 | 目标章节数 | 20 |
| chapter_word_min | number | 否 | 每章最少字数 | 800 |
| chapter_word_max | number | 否 | 每章最多字数 | 1000 |

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
  "character_initial_state": {
    "江念": {
      "location": "城市",
      "occupation": "普通职员",
      "met_people": ["顾辞", "沈离"],  // 已认识丈夫和白月光
      "status": "已婚",
      "key_attributes": ["深情", "付出型人格"],
      "relationships": {
        "顾辞": {
          "current_state": "深爱",
          "intensity": 9,
          "history": []
        },
        "沈离": {
          "current_state": "表面友好",
          "intensity": 5,
          "history": []
        }
      },
      "emotional_baseline": "期待、深情",
      "character_arc_stage": "start"
    },
    "顾辞": {
      "location": "城市",
      "occupation": "公司高管",
      "met_people": ["江念", "沈离"],  // 已认识妻子和白月光
      "status": "已婚",
      "key_attributes": ["冷漠", "事业心强"],
      "relationships": {
        "江念": {
          "current_state": "冷漠",
          "intensity": 3,
          "history": []
        },
        "沈离": {
          "current_state": "偏爱",
          "intensity": 7,
          "history": []
        }
      },
      "emotional_baseline": "冷漠、轻视",
      "character_arc_stage": "start"
    },
    "沈离": {
      "location": "城市",
      "occupation": "无业",
      "met_people": ["顾辞", "江念"],  // 已认识男主和女主
      "status": "单身",
      "key_attributes": ["心机", "表面柔弱"],
      "relationships": {
        "顾辞": {
          "current_state": "暧昧",
          "intensity": 7,
          "history": []
        },
        "江念": {
          "current_state": "嫉妒",
          "intensity": 6,
          "history": []
        }
      },
      "emotional_baseline": "嫉妒、算计",
      "character_arc_stage": "start"
    }
  },
  "character_arc_mapping": {
    "江念": {
      "start": {
        "chapters": [1, 2, 3, 4, 5],
        "state": "深情付出",
        "behavior_guidelines": ["默默等待", "委曲求全", "关心对方"],
        "taboo_behaviors": ["冷漠", "反击", "决绝"]
      },
      "middle": {
        "chapters": [6, 7, 8, 9, 10, 11, 12, 13, 14, 15],
        "state": "心死觉醒",
        "behavior_guidelines": ["冷静", "筹划", "反击", "不再期待"],
        "taboo_behaviors": ["深情付出", "原谅太快", "软弱"]
      },
      "end": {
        "chapters": [16, 17, 18, 19, 20],
        "state": "独立女王",
        "behavior_guidelines": ["果断", "强势", "事业为重", "打脸"],
        "taboo_behaviors": ["犹豫", "心软", "回头"]
      },
      "transition_points": {
        "chapter_5_to_6": {
          "from_stage": "start",
          "to_stage": "middle",
          "trigger_event": "彻底心死，决定反击",
          "emotional_change": "深情付出 → 心死觉醒"
        },
        "chapter_15_to_16": {
          "from_stage": "middle",
          "to_stage": "end",
          "trigger_event": "反击成功，开始独立",
          "emotional_change": "心死觉醒 → 独立女王"
        }
      }
    },
    "顾辞": {
      "start": {
        "chapters": [1, 2, 3, 4, 5],
        "state": "冷漠轻视",
        "behavior_guidelines": ["轻视女主", "偏爱白月光", "不耐烦"],
        "taboo_behaviors": ["关心女主", "后悔", "觉醒"]
      },
      "middle": {
        "chapters": [6, 7, 8, 9, 10, 11, 12, 13, 14, 15],
        "state": "幡然醒悟",
        "behavior_guidelines": ["焦虑", "追妻", "卑微", "后悔"],
        "taboo_behaviors": ["仍冷漠", "不作为", "傲慢"]
      },
      "end": {
        "chapters": [16, 17, 18, 19, 20],
        "state": "后悔追妻",
        "behavior_guidelines": ["跪舔", "疯狂弥补", "崩溃"],
        "taboo_behaviors": ["仍傲慢", "不承认错误", "放弃"]
      },
      "transition_points": {
        "chapter_5_to_6": {
          "from_stage": "start",
          "to_stage": "middle",
          "trigger_event": "发现女主真的要离婚",
          "emotional_change": "冷漠轻视 → 开始觉醒"
        }
      }
    },
    "沈离": {
      "start": {
        "chapters": [1, 2, 3, 4, 5],
        "state": "表面柔弱",
        "behavior_guidelines": ["装柔弱", "挑拨", "示威"],
        "taboo_behaviors": ["直接攻击", "暴露心机"]
      },
      "middle": {
        "chapters": [6, 7, 8, 9, 10, 11, 12, 13, 14, 15],
        "state": "心机深沉",
        "behavior_guidelines": ["暗中算计", "陷害", "不择手段"],
        "taboo_behaviors": ["装柔弱", "退缩"]
      },
      "end": {
        "chapters": [16, 17, 18, 19, 20],
        "state": "被打脸",
        "behavior_guidelines": ["震惊", "崩溃", "后悔"],
        "taboo_behaviors": ["仍然嚣张", "反击成功"]
      }
    }
  },
  "content_type": "outline",
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "./outputs/[书名]/work/",
  "reference_path": "./docs/参考资料.md"
}
```

## 节奏铁律模板（支持可变章节数）

### 阶段分配原则

根据总章节数动态分配阶段比例：

| 总章节数 | 开篇冲突 | 小爽点 | 升级打脸 | 高潮决战 | 完美结局 |
|----------|----------|--------|----------|----------|----------|
| 5-10章 | 第1章 | 第2-3章 | 第4-(n-2)章 | 第(n-1)章 | 第n章 |
| 11-20章 | 第1章 | 第2-5章 | 第6-(n-3)章 | 第(n-2)-章(n-1)章 | 第n章 |
| 21-50章 | 第1-2章 | 第3-8章 | 第9-(n-3)章 | 第(n-2)-章(n-1)章 | 第n章 |
| 51+章 | 第1-3章 | 第4-10章 | 第11-(n-4)章 | 第(n-3)-章(n-1)章 | 第n章 |

### 第 1 章（开篇冲突）
- **要求**：300 字内出冲突/背叛/死亡/觉醒
- **字数**：根据 `chapter_word_min` 和 `chapter_word_max` 配置
- **钩子**：强悬念/反转

### 小爽点阶段（第 2-5 章，根据章节数动态调整）
- **要求**：初步打脸、小爽点密集
- **字数**：根据配置的字数范围
- **钩子**：悬念/危机/反转

### 升级打脸阶段（第 6-(n-3) 章，根据章节数动态调整）
- **要求**：剧情反转、感情升温、升级打脸
- **字数**：根据配置的字数范围
- **钩子**：强钩子（每章必备）

### 高潮决战阶段（第 (n-2)-(n-1) 章，根据章节数动态调整）
- **要求**：终极打脸、高潮对决
- **字数**：根据配置的字数范围
- **钩子**：最强钩子

### 第 n 章（完美结局，n为总章节数）
- **要求**：完美结局、爽点拉满、逻辑闭环
- **字数**：根据配置的字数范围
- **钩子**：无（结局）

### 阶段分配计算示例

**示例1：5章短视频剧本**
```
第1章：开篇冲突
第2-3章：小爽点不断
第4章：升级打脸
第5章：完美结局
```

**示例2：10章中短篇**
```
第1章：开篇冲突
第2-3章：小爽点不断
第4-8章：升级打脸
第9章：高潮决战
第10章：完美结局
```

**示例3：20章短篇**
```
第1章：开篇冲突
第2-5章：小爽点不断
第6-17章：升级打脸
第18-19章：高潮决战
第20章：完美结局
```

**示例4：50章中篇**
```
第1-2章：开篇冲突
第3-8章：小爽点不断
第9-47章：升级打脸
第48-49章：高潮决战
第50章：完美结局
```

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
   - 读取配置参数（target_chapters, chapter_word_min, chapter_word_max）

2. **动态计算阶段分配**
   - 根据总章节数计算各阶段起止位置
   - 参考上面的"阶段分配原则"表格
   - 计算每个阶段的章节数比例

3. **按节奏铁律分配章节**
   - 开篇冲突：第1章（或1-2章）
   - 小爽点：根据章节数动态确定
   - 升级打脸：占中间大部分章节
   - 高潮决战：最后2-3章
   - 完美结局：最后一章

4. **设计每章大纲**
   - 章节标题
   - 章节摘要
   - 字数目标（根据配置动态设定）
   - 关键情节点
   - 章末钩子类型

5. **生成 JSON 输出**
   - 完整大纲（根据配置的章节数）
   - 人物弧光
   - 时间线分配
   - **【新增】人物初始状态**（供章节写作时追踪）

 6. **生成人物初始状态**（重要！）
    - 根据选题阶段的人物设定，生成初始状态
    - 包含：location（当前位置）、occupation（职业）、met_people（已认识的人）、status（婚姻/身份状态）、key_attributes（关键属性）
    - 【新增】relationships（初始关系状态）、emotional_baseline（情感基线）、character_arc_stage（当前弧光阶段）
    - 这个状态会在章节写作过程中被不断更新，确保人物关系逻辑一致

 7. **生成人物弧光映射**（重要！）
    - 根据总章节数动态分配每个弧光阶段对应的章节
    - 为每个阶段定义行为指南（behavior_guidelines）和禁忌行为（taboo_behaviors）
    - 明确转折点（transition_points）及触发事件
    - 确保人物行为在全文中符合弧光轨迹

## 注意事项

 1. 根据配置控制每章字数（默认800-1000字）
 2. 根据配置控制总章节数（默认20章）
 3. 开篇 300 字内必出冲突
 4. 每章结尾必须有强钩子
 5. 核心冲突三要素贯穿全文
 6. 人物弧光完整、合理
 7. 动态分配各阶段章节比例
 8. **【新增】人物弧光映射必须明确每个阶段对应的章节**
 9. **【新增】人物初始状态必须包含关系状态和情感基线**
 10. **【新增】转折点必须有明确的触发事件和情感变化描述**
