---
name: novel-step-04-chapter-writer
description: 章节写手 - 逐章执行大纲，协调子技能完成单章写作，支持可变章节数
---

# 章节写手

## 职责

逐章执行大纲，协调调用子技能完成单章写作。支持灵活的章节数和字数配置。

## 输入格式

```json
{
  "chapter_number": 1,
  "outline": {
    "total_chapters": 20,
    "chapter_word_min": 800,
    "chapter_word_max": 1000,
    "category": "女频-追妻火葬场",
    "chapters": [...],
    "character_arc": {...},
    "character_initial_state": {...}  // 【新增】人物初始状态
  },
  "previous_chapters": [],  // 已写章节
  "character_state_tracker": {  // 【新增】人物状态追踪器
    "江念": {
      "met_people": ["顾辞", "沈离"],
      "relationships": {
        "顾辞": {
          "current_state": "仇视",
          "intensity": 8,
          "history": [
            {"chapter": 1, "from": "深爱", "to": "心死", "trigger": "目睹出轨"}
          ]
        },
        "沈离": {
          "current_state": "敌对",
          "intensity": 7,
          "history": [
            {"chapter": 1, "from": "陌生", "to": "敌对", "trigger": "确认情敌"}
          ]
        }
      },
      "emotional_trajectory": {
        "chapter_1": "期待 → 震惊 → 痛苦 → 心死 → 觉醒"
      },
      "character_arc_stage": "middle",
      "key_events_impact": [
        {"event": "目睹丈夫抱白月光", "impact": "对顾辞完全死心"}
      ]
    },
    "顾辞": {
      "met_people": ["江念"],
      "relationships": {
        "江念": {
          "current_state": "冷漠",
          "intensity": 3,
          "history": []
        }
      },
      "emotional_trajectory": {
        "chapter_1": "冷漠 → 不耐烦"
      },
      "character_arc_stage": "start",
      "key_events_impact": []
    }
  },
  "character_arc_mapping": {  // 【新增】人物弧光映射
    "江念": {
      "start": {"chapters": [1, 2, 3, 4, 5], "state": "深情付出"},
      "middle": {"chapters": [6, 7, 8, 9, 10, 11, 12, 13, 14, 15], "state": "心死觉醒"},
      "end": {"chapters": [16, 17, 18, 19, 20], "state": "独立女王"}
    }
  },
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "./outputs/[书名]/work/",
  "reference_path": "./docs/参考资料.md"
}
```

### 【关键】输出路径规则

- **【必须】** 所有临时文件必须输出到 `work_dir` 目录下
- **【禁止】** 禁止将临时文件输出到 `outputs/` 根目录
- **临时文件包括**：
  - temp_chapter_1.md, temp_chapter_2.md 等
  - 任何中间处理文件

### 输入参数说明

| 参数 | 类型 | 必填 | 说明 | 默认值 |
|------|------|--------|------|--------|
| chapter_number | number | 否 | 当前章节号 | - |
| outline.total_chapters | number | 否 | 总章节数 | 20 |
| outline.chapter_word_min | number | 否 | 每章最少字数 | 800 |
| outline.chapter_word_max | number | 否 | 每章最多字数 | 1000 |

## 输出格式

```json
{
  "chapter_number": 1,
  "chapter_title": "ICU 门外",
  "chapter_content": "章节正文内容...",
  "word_count": 856,
  "ending_hook": "睁开眼，发现自己回到了三年前的婚礼前夕",
  "hook_type": "悬念",
  "character_state_tracker": {  // 【新增】本章结束后的人物状态
    "江念": {
      "met_people": ["顾辞", "沈离", "护士"],
      "relationships": {
        "顾辞": {
          "current_state": "仇视",
          "intensity": 9,
          "history": [
            {"chapter": 1, "from": "深爱", "to": "心死", "trigger": "目睹出轨"}
          ]
        },
        "沈离": {
          "current_state": "敌对",
          "intensity": 7,
          "history": [
            {"chapter": 1, "from": "陌生", "to": "敌对", "trigger": "确认情敌"}
          ]
        },
        "护士": {
          "current_state": "陌生",
          "intensity": 1,
          "history": []
        }
      },
      "emotional_trajectory": {
        "chapter_1": "期待 → 震惊 → 痛苦 → 心死 → 觉醒"
      },
      "character_arc_stage": "middle",
      "key_events_impact": [
        {"event": "目睹丈夫抱白月光", "impact": "对顾辞完全死心"},
        {"event": "提出离婚被冷漠对待", "impact": "彻底绝望，决定重生"}
      ]
    },
    "顾辞": {
      "met_people": ["江念", "沈离"],
      "relationships": {
        "江念": {
          "current_state": "冷漠",
          "intensity": 3,
          "history": []
        },
        "沈离": {
          "current_state": "亲密",
          "intensity": 7,
          "history": []
        }
      },
      "emotional_trajectory": {
        "chapter_1": "冷漠 → 不耐烦"
      },
      "character_arc_stage": "start",
      "key_events_impact": []
    }
  },
  "meta": {
    "author_draft_words": 950,
    "hook_crafted": true,
    "word_adjusted": true,
    "editor_score": 9.2,
    "reader_score": 8.8
  }
}
```

## 内部调用顺序

 1. **novel-sub-skill-novelist-author** → 作家写作初稿
 2. **novel-sub-skill-word-count-controller** → 控制字数（总字数800-1000）
 3. **novel-sub-skill-hook-crafter** → 设计章末钩子（添加50-100字）
 4. **novel-sub-skill-character-consistency-checker** → 人物一致性检查（新增）
 5. **novel-step-03-editor-reviewer** → 质检
 6. **novel-sub-skill-reader-feedback** → 读者反馈

## 【关键】输出文件要求

**每章完成后必须输出文件到 `work_dir` 目录（完整路径：`./outputs/[书名]/work/`）**：

1. **章节内容文件**：`temp_chapter_XX.md`（XX为章节号，完整路径：`./outputs/[书名]/work/temp_chapter_XX.md`）
2. **章节元数据文件**：`temp_chapter_XX_meta.json`（完整路径：`./outputs/[书名]/work/temp_chapter_XX_meta.json`）
3. **状态历史备份文件**：`character_state_history.json`（完整路径：`./outputs/[书名]/work/character_state_history.json`，每章完成后更新）

**元数据文件必须包含**：
```json
{
  "chapter_number": 1,
  "chapter_title": "章节标题",
  "chapter_content": "章节正文...",
  "word_count": 728,
  "ending_hook": "章末钩子内容",
  "hook_type": "悬念",
  "character_state_tracker": {...},  // 必须包含人物状态
  "meta": {...},
  "status": "completed"
}
```

**【关键】人物状态持久化**：
- 每章的 `character_state_tracker` 必须写入 `temp_chapter_XX_meta.json`
- 下一章读取时，从上一章的 meta 文件中加载 `character_state_tracker`
- 不能仅依赖内存传递，必须有文件持久化

## target_audience 提取逻辑

从 `outline.category` 中提取目标读者群体：
- `category` 以 "女频-" 开头 → `target_audience: "female"`
- `category` 以 "男频-" 开头 → `target_audience: "male"`
- `category` 以 "全频-" 开头 → `target_audience: "all"`

## 子技能调用说明

**【关键】章节标题统一规则**：
- **必须**从 `outline.chapters[chapter_number - 1].title` 读取章节标题
- **禁止**从其他来源（如作家输出、钩子输出等）获取标题
- 所有子技能的输入都必须包含正确的章节标题
- 最终输出的章节文件名、元数据、小说全文都使用该标题

### 1. novelist-author（作家）

**【重要】** 临时文件输出到 `work_dir` 目录

输入：
```json
{
  "chapter_outline": {
    "chapter_number": 1,
    "title": "ICU 门外",
    "summary": "女主江念在 ICU 门外等顾辞...",
    "key_plot_points": [...]
  },
  "character_arc": {...},
  "characters": {...},
  "character_state_tracker": {...},  // 【新增】人物状态追踪器，包含截至上一章的人物关系
  "previous_content": "前文内容...",
  "target_tone": "虐恋爽感",
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "./outputs/[书名]/work/"
```

**【关键】输出路径规则（所有技能必须统一）**：
- 工作目录：`./outputs/[书名]/work/`
- 参考资料：`./docs/参考资料.md`
- 输出目录：`./outputs/`
- 小说目录：`./outputs/[书名]/`
- 临时文件必须输出到 `work_dir`，禁止输出到 `outputs/` 根目录
}
```

**【关键】人物状态追踪器使用说明**：
- `character_state_tracker` 包含截至上一章结束时的人物状态
- 作家在写作时必须检查：如果是"初见"某人物，必须确保该人物在 `met_people` 中不存在
- 如果人物已在 `met_people` 中，则不应使用"第一次见面"、"初次相见"等描述
- 本章新增的人物互动需要记录到输出的 `character_state_update` 中

**【重要】character_state_tracker 完整结构要求**：
```json
{
  "人物名": {
    "met_people": ["已见过的人1", "已见过的人2"],
    "relationships": {
      "其他人物名": {
        "current_state": "关系状态",
        "intensity": 1-10,
        "history": [
          {"chapter": 1, "from": "旧状态", "to": "新状态", "trigger": "触发事件"}
        ]
      }
    },
    "emotional_trajectory": {
      "chapter_1": "情感变化轨迹描述"
    },
    "character_arc_stage": "start/middle/end",
    "key_events_impact": [
      {"event": "事件描述", "impact": "对人物的影响"}
    ]
  }
}
```

**【重要】状态历史备份机制**（完整路径说明）：
- 状态历史文件：`./outputs/[书名]/work/character_state_history.json`
- 每章完成后必须备份人物状态到该文件
- 如果下一章读取 meta 文件失败，从该历史文件恢复
- 容错恢复机制确保流程不会中断
- 状态历史文件格式：
  ```json
  {
    "last_updated_chapter": 1,
    "states": {
      "chapter_1": {...人物状态...},
      "chapter_2": {...人物状态...}
    }
  }
  ```

输出：
```json
{
  "draft_content": "初稿内容...",
  "draft_word_count": 950,
  "characters_involved": ["江念", "顾辞"]
}
```

### 2. word-count-controller（字数控制）

输入：
```json
{
  "content": "初稿内容...",
  "target_min": 800,  // 从 outline.chapter_word_min 读取
  "target_max": 1000, // 从 outline.chapter_word_max 读取
  "current_words": 950
}
```

输出：
```json
{
  "adjusted_content": "调整后内容...",
  "actual_words": 856,
  "adjustment_type": "trim"
}
```

**注意**：target_min 和 target_max 从 outline 配置中动态读取，不再硬编码为 800-1000。

### 3. hook-crafter（钩子师）

输入：
```json
{
  "chapter_content": "调整后内容...",
  "next_chapter_outline": {
    "chapter_number": 2,
    "title": "婚礼前夕"
  },
  "target_hook_type": "悬念"
}
```

输出：
```json
{
  "hook_text": "睁开眼，发现自己回到了三年前的婚礼前夕",
  "hook_type": "悬念",
  "hook_strength": "强",
  "final_content": "章节内容 + 钩子"
}
```

### 4. character-consistency-checker（人物一致性检查）【新增】

**【重要】** 确保人物行为、情感、关系状态逻辑连贯

输入：
```json
{
  "chapter_number": 1,
  "chapter_content": "完整章节内容...",
  "previous_chapters": [],
  "character_state_tracker": {...},
  "character_arc_mapping": {...},
  "session_id": "uuid-v4",
  "work_dir": "./outputs/[书名]/work/"
```

**【关键】输出路径规则（所有技能必须统一）**：
- 工作目录：`./outputs/[书名]/work/`
- 参考资料：`./docs/参考资料.md`
- 输出目录：`./outputs/`
- 小说目录：`./outputs/[书名]/`
- 临时文件必须输出到 `work_dir`，禁止输出到 `outputs/` 根目录
}
```

输出：
```json
{
  "status": "pass | needs_revision",
  "score": 9.2,
  "checks": {
    "behavior_consistency": {"status": "pass", "score": 9.5},
    "relationship_progression": {"status": "pass", "score": 9.0},
    "emotional_continuity": {"status": "pass", "score": 9.3},
    "memory_consistency": {"status": "pass", "score": 9.2},
    "dialogue_appropriateness": {"status": "pass", "score": 9.0}
  },
  "issues": [],
  "character_state_update": {...},
  "suggestions": []
}
```

**处理逻辑**：
- 如果 status 为 "needs_revision"，根据 suggestions 修改章节内容
- 最多重试 3 次
- 每次检查后更新 character_state_tracker

### 5. editor-reviewer（编辑审核）

输入：
```json
{
  "content_type": "chapter",
  "chapter_number": 1,
  "chapter_content": "完整章节内容...",
  "chapter_outline": {...},
  "target_words": 800
}
```

输出：
```json
{
  "status": "pass",
  "score": 9.2,
  "issues": [],
  "checklist": {...}
}
```

### 6. reader-feedback（读者反馈）

**【重要】** 临时文件输出到 `work_dir` 目录

输入：
```json
{
  "content": "完整章节内容...",
  "target_audience": "female | male | all",  // 从category提取
  "target_tone": "虐恋爽感",
  "chapter_number": 1,
  "work_dir": "./outputs/[书名]/work/"
```

**【关键】输出路径规则（所有技能必须统一）**：
- 工作目录：`./outputs/[书名]/work/`
- 参考资料：`./docs/参考资料.md`
- 输出目录：`./outputs/`
- 小说目录：`./outputs/[书名]/`
- 临时文件必须输出到 `work_dir`，禁止输出到 `outputs/` 根目录
}
```

输出：
```json
{
  "score": 8.8,
  "high_points": [
    "开篇冲突强烈",
    "女主性格鲜明"
  ],
  "low_points": [
    "部分对话略显生硬"
  ],
  "overall": "章节质量优秀，符合目标读者期待"
}
```

## 【关键】章节必须顺序执行，禁止并行

**【绝对禁止】**：
- ❌ 禁止使用 subagent/parallel agents 同时写多章
- ❌ 禁止开启多个线程/进程并行写章节
- ❌ 禁止一次生成多章内容
- ✅ 必须严格按章节顺序：第1章 → 第2章 → 第3章 → ... → 第N章

**【必须遵守】**：
1. 第1章必须完全写完（包括质量审核通过）后才能开始第2章
2. 每一章都必须基于上一章的 `previous_content` 和 `character_state_tracker`
3. 人物状态必须逐章累积传递

## 循环执行

章节写手需要**严格顺序**执行：

```
total_chapters = outline.total_chapters  # 从配置读取，默认20
work_dir = outline.work_dir  # 工作目录

# 【第1章特殊处理】
chapter = 1
# 第1章：使用大纲中的初始状态
character_state_tracker = outline.character_initial_state
character_arc_mapping = outline.character_arc_mapping

# 【新增】初始化状态历史备份
init_state_history(work_dir)

# 加载前文内容
previous_content = load_previous_chapters(work_dir, chapter)

# 写入本章
result = write_chapter(
    chapter_number=chapter,
    outline=outline,
    previous_content=previous_content,
    character_state_tracker=character_state_tracker,
    character_arc_mapping=character_arc_mapping
)

# 【必须】保存本章meta（含人物状态）+ 备份状态历史
save_chapter_meta(work_dir, chapter, result)
backup_state_history(work_dir, chapter, result.character_state_tracker)

# 更新状态
character_state_tracker = merge_state_tracker(character_state_tracker, result.character_state_update)

# 【第2-N章：必须从上一章的meta文件读取状态】
for chapter in 2..total_chapters:
    # 【关键】必须从上一章的meta文件读取人物状态
    prev_meta = load_chapter_meta(work_dir, chapter - 1)
    
    # 【新增】容错处理：如果文件读取失败，从状态历史恢复
    if prev_meta is None:
        prev_meta = recover_from_history(work_dir, chapter - 1)
        if prev_meta is None:
            raise Error("无法恢复人物状态，流程中断")
    
    character_state_tracker = prev_meta.character_state_tracker

    # 加载前文内容（所有已完成的章节）
    previous_content = load_previous_chapters(work_dir, chapter)

    # 写入本章（传入从文件读取的状态）
    result = write_chapter(
        chapter_number=chapter,
        outline=outline,
        previous_content=previous_content,
        character_state_tracker=character_state_tracker,
        character_arc_mapping=character_arc_mapping
    )

    # 【必须】保存本章meta（含更新后的人物状态）+ 备份状态历史
    save_chapter_meta(work_dir, chapter, result)
    backup_state_history(work_dir, chapter, result.character_state_tracker)

    # 更新累积状态（供下一章使用）
    character_state_tracker = merge_state_tracker(character_state_tracker, result.character_state_update)

    # 必须等本章审核通过后才能进入下一章
```

**【关键】人物状态追踪逻辑**：
1. **第1章**：使用 `outline.character_initial_state` 作为初始状态
2. **第2-N章**：**必须**从 `work_dir/temp_chapter_{N-1}_meta.json` 读取 `character_state_tracker`
3. 每章写完后，保存本章的 `character_state_tracker` 到 `temp_chapter_XX_meta.json`
4. 合并 `character_state_update` 到累积状态，供下一章从文件读取

### 循环示例

**示例1：5章短视频剧本**
```
for chapter in 1..5:
    result = chapter_writer(chapter, outline, previous_chapters)
    previous_chapters.append(result)
```

**示例2：20章短篇**
```
for chapter in 1..20:
    result = chapter_writer(chapter, outline, previous_chapters)
    previous_chapters.append(result)
```

**示例3：50章中篇**
```
for chapter in 1..50:
    result = chapter_writer(chapter, outline, previous_chapters)
    previous_chapters.append(result)
```

## 质量控制

### 审核不通过处理

```json
{
  "status": "needs_revision",
  "suggestions": [
    "章末钩子需增强悬念感",
    "第 3 段建议删除水文"
  ]
}
```

处理策略：
1. 根据编辑建议修改章节
2. 重新调用 editor-reviewer 审核
3. 最多重试 3 次，仍未通过则标记失败

### 字数不达标处理

```json
{
  "actual_words": 720,
  "target_min": 800
}
```

处理策略：
1. 调用 novel-sub-skill-word-count-controller 扩充或精简
2. 确保在 800-1000 字范围内范围内
3. 保留核心情节和钩子

## 注意事项

 1. 严格按照大纲执行
 2. 保持人物性格一致
 3. 每章必须有强钩子
 4. 字数根据配置动态控制（默认800-1000字）
 5. 支持可变章节数（默认20章）
 6. 支持 JSON 输出便于后续步骤
 7. 子技能调用失败需有降级处理
 8. **【新增】必须调用 character-consistency-checker 检查人物一致性**
 9. **【新增】状态持久化必须有备份机制和容错恢复**

 ## 【新增】状态持久化辅助函数

 ### 文件操作函数

 **init_state_history(work_dir)**
 - 初始化状态历史备份文件 `character_state_history.json`
 - 如果文件不存在，创建空的历史记录结构

 **save_chapter_meta(work_dir, chapter_num, result)**
 - 保存章节元数据到 `temp_chapter_XX_meta.json`
 - 包含完整的章节信息和人物状态

 **backup_state_history(work_dir, chapter_num, state_tracker)**
 - 备份人物状态到 `character_state_history.json`
 - 每章完成后都备份，用于容错恢复

 **load_chapter_meta(work_dir, chapter_num)**
 - 从文件读取章节元数据
 - 如果文件不存在返回 None

 **recover_from_history(work_dir, chapter_num)**
 - 从状态历史恢复人物状态
 - 用于主文件读取失败时的容错恢复

 ### 状态合并函数

 **merge_state_tracker(old_state, update)**
 - 合并人物状态更新到现有状态
 - 深度合并 relationships、emotional_trajectory、key_events_impact
 - 使用集合去重 met_people

 ### 调用示例

 ```python
 # 初始化
 init_state_history("./outputs/novel/work/")

 # 保存章节
 save_chapter_meta("./outputs/novel/work/", 1, chapter_result)
 backup_state_history("./outputs/novel/work/", 1, chapter_result.character_state_tracker)

 # 读取章节
 prev_meta = load_chapter_meta("./outputs/novel/work/", 1)
 if prev_meta is None:
     prev_meta = recover_from_history("./outputs/novel/work/", 1)

 # 合并状态
 new_state = merge_state_tracker(old_state, update_data)
 ```
