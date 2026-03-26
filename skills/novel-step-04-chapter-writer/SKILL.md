---
name: novel-step-04-chapter-writer
description: 章节写手 - 逐章执行大纲，协调子技能完成单章写作，支持可变章节数
---

# 章节写手

## 【执行模式】必须单步顺序执行

**【关键】禁止任何形式的并发/批量执行**：
- 每次只处理一个章节
- 完成当前章节的所有子技能后才能进入下一章
- 不得批量生成多章节内容
- 不得使用 subagent/parallel agents
- 不得跳过任何子技能

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
  "character_state_tracker": {  // 【新增】人物状态追踪器（第1章初始状态）
    "江念": {
      "met_people": ["顾辞", "沈离"],  // 已认识丈夫和白月光
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
      "emotional_trajectory": {},
      "character_arc_stage": "start",  // 第1-5章为start阶段
      "key_events_impact": []
    },
    "顾辞": {
      "met_people": ["江念", "沈离"],  // 已认识妻子和白月光
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
      "emotional_trajectory": {},
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
  "character_state_tracker": {  // 【新增】本章结束后的人物状态（第1章完成后的状态）
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
      "character_arc_stage": "start",  // 第1-5章为start阶段，第6章后才变为middle
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

**【警告】这是强制性规则，违反会导致数据损坏和逻辑错误**

**【绝对禁止】**：
- ❌ 禁止使用 subagent/parallel agents 同时写多章
- ❌ 禁止开启多个线程/进程并行写章节
- ❌ 禁止一次生成多章内容
- ❌ 禁止批量生成章节后再补充子技能
- ✅ 必须严格按章节顺序：第1章 → 第2章 → 第3章 → ... → 第N章

**【必须遵守的执行流程】**：
每章都必须完整执行以下所有步骤后，才能开始下一章：

```
第N章完整流程：
1. 调用 novelist-author（作家）写入初稿
   - 等待结果返回
   - 验证输出格式正确

2. 调用 word-count-controller（字数控制）
   - 等待结果返回
   - 验证字数在范围内

3. 调用 hook-crafter（钩子师）
   - 等待结果返回
   - 验证钩子强度 >= 8.0

4. 调用 character-consistency-checker（人物一致性检查）
   - 等待结果返回
   - 检查状态更新

5. 保存 temp_chapter_XX.md 文件（必须完成）
   - 写入文件到 work_dir
   - 验证文件存在

6. 保存 temp_chapter_XX_meta.json 文件（必须完成）
   - 包含 character_state_tracker
   - 写入文件到 work_dir
   - 验证文件存在

7. 调用 editor-reviewer（编辑审核）
   - 等待结果返回
   - 如果不通过，重新生成本章
   - 最多重试3次

8. 调用 reader-feedback（读者反馈）
   - 等待结果返回
   - 验证评分 >= 8.0

9. 更新 character_state_history.json（必须完成）
   - 备份当前章节状态
   - 验证文件存在

【第N章完成】→ 【才能开始第N+1章】
```

**【必须遵守】**：
1. 第1章必须完全写完（包括质量审核通过）后才能开始第2章
2. 每一章都必须基于上一章的 `previous_content` 和 `character_state_tracker`
3. 人物状态必须逐章累积传递
4. 每章必须保存两个文件：`temp_chapter_XX.md` 和 `temp_chapter_XX_meta.json`
5. 每章必须更新 `character_state_history.json` 备份
6. **禁止跳过任何子技能或文件保存步骤**

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

# 【重要】第1章的 previous_content 为空（因为没有前文）
previous_content = ""  # 或 null

# 【新增】初始化状态历史备份
init_state_history(work_dir)

# 写入本章
result = write_chapter(
    chapter_number=chapter,
    outline=outline,
    previous_content=previous_content,  # 第1章传入空字符串
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
    # 【重要】previous_content 包含所有已完成的章节，从第1章到第N-1章
    previous_content = load_previous_chapters(work_dir, chapter)

    # 【重要】确保 previous_content 不为空（第2章起应该有内容）
    if previous_content == "" or previous_content is None:
        raise Error(f"第{chapter}章无法加载前文内容，流程中断")

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

## 【关键】子技能完整资源引用规则

**必须传递给 novelist-author 的所有资源**：
```json
{
  "chapter_outline": {...},  // 从 outline.chapters[chapter_number - 1] 读取
  "character_arc": {...},  // 从 outline.character_arc 读取
  "characters": {...},  // 从 outline.characters 读取
  "character_state_tracker": {...},  // 逐章累积传递
  "character_arc_mapping": {...},  // 从 outline.character_arc_mapping 读取
  "previous_content": "所有已完成的章节",  // 第1章为空字符串
  "target_tone": "...",  // 从 outline.tone 或根据 category 推导
  "work_dir": "./outputs/[书名]/work/"
}
```

**必须传递给 hook-crafter 的所有资源**：
```json
{
  "chapter_content": "章节内容...",  // 从作家输出获取
  "chapter_number": N,  // 当前章节号
  "current_hook_type": "悬念",  // 从 outline.chapters[N-1].ending_hook_type 读取
  "next_chapter_outline": {...},  // 从 outline.chapters[N] 读取下一章大纲（最后一章除外）
  "target_tone": "...",  // 与基调一致
  "work_dir": "./outputs/[书名]/work/"
}
```

**必须传递给 word-count-controller 的所有资源**：
```json
{
  "content": "章节内容...",  // 从作家输出获取
  "target_min": 800,  // 从 outline.chapter_word_min 读取
  "target_max": 1000,  // 从 outline.chapter_word_max 读取
  "current_words": 950,  // 计算实际字数
  "work_dir": "./outputs/[书名]/work/"
}
```

**必须传递给 character-consistency-checker 的所有资源**：
```json
{
  "chapter_number": N,
  "chapter_content": "章节内容...",  // 从作家输出获取
  "previous_chapters": [...],  // 所有已完成的章节
  "character_state_tracker": {...},  // 逐章累积传递
  "character_arc_mapping": {...},  // 从 outline.character_arc_mapping 读取
  "work_dir": "./outputs/[书名]/work/"
}
```

**必须传递给 editor-reviewer 的所有资源**：
```json
{
  "content_type":": "chapter",
  "chapter_number": N,
  "chapter_content": "章节内容...",  // 从作家输出获取
  "chapter_outline": {...},  // 从 outline.chapters[N-1] 读取
  "target_words": 800,  // 从 outline.chapter_word_min/max 计算
  "work_dir": "./outputs/[书名]/work/"
}
```

**必须传递给 reader-feedback 的所有资源**：
```json
{
  "feedback_type": "chapter",
  "chapter_number": N,
  "chapter_content": "章节内容...",  // 从作家输出获取
  "target_audience": "female",  // 从 category 提取
  "target_tone": "...",  // 与基调一致
  "work_dir": "./outputs/[书名]/work/"
}
```

## 【关键】资源加载和验证规则

**1. 加载 previous_content 的规则**：
```python
def load_previous_chapters(work_dir, chapter_number):
    """
    加载所有已完成的章节内容

    chapter_number=1: 返回空字符串 "" （因为没有前文）
    chapter_number=2: 读取 temp_chapter_1.md
    chapter_number=3: 读取 temp_chapter_1.md + temp_chapter_2.md
    ...
    """
    if chapter_number == 1:
        return ""  # 第1章没有前文

    previous_content = []
    for i in range(1, chapter_number):
        file_path = f"{work_dir}/temp_chapter_{i}.md"
        if not os.path.exists(file_path):
            raise Error(f"章节文件 {file_path} 不存在，无法加载前文")
        with open(file_path, 'r', encoding='utf-8') as f:
            content = f.read()
            previous_content.append(content)

    return "\n\n".join(previous_content)
```

**2. 验证 previous_content 加载成功**：
```python
# 在 write_chapter 函数调用之前验证
if chapter_number > 1 and not previous_content:
    raise Error(f"第{chapter_number}章：无法加载前文内容，流程中断")

# 验证前文内容长度（可选）
if chapter_number > 1 and len(previous_content) < 100:
    print(f"警告：第{chapter_number}章的前文内容过短，可能影响连贯性")
```

**3. 验证 outline 完整性**：
```python
def validate_outline(outline, total_chapters):
    """
    验证 outline 包含所有必要的资源
    """
    required_fields = [
        "total_chapters",
        "chapter_word_min",
        "chapter_word_max",
        "chapters",
        "character_arc",
        "characters",
        "character_initial_state",
        "character_arc_mapping"
    ]

    for field in required_fields:
        if field not in outline:
            raise Error(f"outline 缺少必需字段：{field}")

    # 验证章节数量
    if len(outline["chapters"]) != total_chapters:
        raise Error(f"章节数量不匹配：期望 {total_chapters}，实际 {len(outline['chapters'])}")
```

**4. 验证 character_state_tracker 完整性**：
```python
def validate_character_state_tracker(character_state_tracker):
    """
    验证人物状态追踪器器包含所有必要字段
    """
    for character_name, state in character_state_tracker.items():
        required_fields = [
            "met_people",
            "relationships",
            "emotional_trajectory",
            "character_arc_stage",
            "key_events_impact"
        ]

        for field in required_fields:
            if field not in state:
                raise Error(f"人物 {character_name} 的状态缺少字段：{field}")
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

 ## 【关键】强制执行检查点

### 每章完成后的检查点

**必须执行以下所有检查，任何检查失败都报错中断流程**：

```python
def verify_chapter_completion(work_dir, chapter_number, outline):
    """
    验证第N章完整完成的所有检查点
    任何检查失败都抛出异常，中断流程
    """

    # 检查1：验证章节文件存在
    chapter_file = f"{work_dir}/temp_chapter_{chapter_number}.md"
    if not os.path.exists(chapter_file):
        raise Error(f"第{chapter_number}章文件不存在：{chapter_file}")

    # 检查2：验证meta文件存在
    meta_file = f"{work_dir}/temp_chapter_{chapter_number}_meta.json"
    if not os.path.exists(meta_file):
        raise Error(f"第{chapter_number}章meta文件不存在：{meta_file}")

    # 检查3：验证状态历史文件存在
    history_file = f"{work_dir}/character_state_history.json"
    if not os.path.exists(history_file):
        raise Error(f"状态历史文件不存在：{history_file}")

    # 检查4：验证meta文件格式正确
    with open(meta_file, 'r', encoding='utf-8') as f:
        meta = json.load(f)

    required_fields = [
        "chapter_number",
        "chapter_title",
        "chapter_content",
        "word_count",
        "character_state_tracker"
    ]

    for field in required_fields:
        if field not in meta:
            raise Error(f"第{chapter_number}章meta缺少字段：{field}")

    # 检查5：验证章节数匹配
    if meta["chapter_number"] != chapter_number:
        raise Error(f"章节数不匹配：期望{chapter_number}，实际{meta['chapter_number']}")

    # 检查6：验证字数在范围内
    word_count = meta.get("word_count", 0)
    target_min = outline.get("chapter_word_min", 800)
    target_max = outline.get("chapter_word_max", 1000)

    if word_count < target_min * 0.9:  # 允许10%误差
        raise Error(f"第{chapter_number}章字数过低：{word_count} < {target_min}")

    if word_count > target_max * 1.1:  # 允许10%误差
        raise Error(f"第{chapter_number}章字数过高：{word_count} > {target_max}")

    # 检查7：验证人物状态完整
    character_state_tracker = meta.get("character_state_tracker", {})
    if not character_state_tracker:
        raise Error(f"第{chapter_number}章人物状态为空")

    for character_name, state in character_state_tracker.items():
        state_fields = ["met_people", "relationships", "character_arc_stage"]
        for field in state_fields:
            if field not in state:
                raise Error(f"人物{character_name}状态缺少字段：{field}")

    # 检查8：验证状态历史包含当前章节
    with open(history_file, 'r', encoding='utf-8') as f:
        history = json.load(f)

    if f"chapter_{chapter_number}" not in history.get("states", {}):
        raise Error(f"状态历史缺少第{chapter_number}章的记录")

    print(f"【验证通过】第{chapter_number}章所有检查点通过")
    return True
```

### 每个子技能调用后的检查点

```python
def verify_skill_completion(skill_name, output, required_fields):
    """
    验证子技能输出完整
    """
    if not output:
        raise Error(f"{skill_name}输出为空")

    for field in required_fields:
        if field not in output:
            raise Error(f"{skill_name}输出缺少字段：{field}")

    print(f"【验证通过】{skill_name}输出完整")
    return True
```

### 顺序执行验证

```python
def verify_sequential_execution(work_dir, current_chapter):
    """
    验证章节按顺序执行
    """
    if current_chapter > 1:
        prev_chapter = current_chapter - 1

        # 检查前一章的meta文件
        prev_meta_file = f"{work_dir}/temp_chapter_{prev_chapter}_meta.json"
        if not os.path.exists(prev_meta_file):
            raise Error(f"第{prev_chapter}章未完成，不能写第{current_chapter}章")

        # 检查前一章的章节文件
        prev_chapter_file = f"{work_dir}/temp_chapter_{prev_chapter}.md"
        if not os.path.exists(prev_chapter_file):
            raise Error(f"第{prev_chapter}章文件不存在，无法加载前文")

    print(f"【验证通过】第{current_chapter}章可执行（前{current_chapter-1}章已完成）")
```

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

```python
def init_state_history(work_dir):
    """
    初始化状态历史备份文件
    """
    history_file = f"{work_dir}/character_state_history.json"
    if not os.path.exists(history_file):
        initial_history = {
            "last_updated_chapter": 0,
            "states": {}
        }
        with open(history_file, 'w', encoding='utf-8') as f:
            json.dump(initial_history, f, ensure_ascii=False, indent=2)
        print(f"【初始化】状态历史文件：{history_file}")

def save_chapter_meta(work_dir, chapter_num, result):
    """
    保存章节元数据到 temp_chapter_XX_meta.json
    """
    meta_file = f"{work_dir}/temp_chapter_{chapter_num}_meta.json"

    meta = {
        "chapter_number": chapter_num,
        "chapter_title": result.get("chapter_title", ""),
        "chapter_content": result.get("chapter_content", ""),
        "word_count": result.get("word_count", 0),
        "ending_hook": result.get("ending_hook", ""),
        "hook_type": result.get("hook_type", ""),
        "character_state_tracker": result.get("character_state_tracker", {}),
        "meta": result.get("meta", {}),
        "status": "completed"
    }

    with open(meta_file, 'w', encoding='utf-8') as f:
        json.dump(meta, f, ensure_ascii=False, indent=2)

    print(f"【保存】章节meta文件：{meta_file}")
    return meta

def backup_state_history(work_dir, chapter_num, state_tracker):
    """
    备份人物状态到 character_state_history.json
    """
    history_file = f"{work_dir}/character_state_history.json"

    # 读取现有历史
    with open(history_file, 'r', encoding='utf-8') as f:
        history = json.load(f)

    # 更新历史
    history["last_updated_chapter"] = chapter_num
    history["states"][f"chapter_{chapter_num}"] = state_tracker

    # 写回文件
    with open(history_file, 'w', encoding='utf-8') as f:
        json.dump(history, f, ensure_ascii=False, indent=2)

    print(f"【备份】状态历史已更新至第{chapter_num}章")
```

### 文件加载函数

**load_chapter_meta(work_dir, chapter_num)**
- 从 `temp_chapter_XX_meta.json` 加载章节元数据
- 用于下一章获取上一章的人物状态

```python
def load_chapter_meta(work_dir, chapter_num):
    """
    加载指定章节的元数据
    """
    meta_file = f"{work_dir}/temp_chapter_{chapter_num}_meta.json"

    if not os.path.exists(meta_file):
        print(f"【警告】元数据文件不存在：{meta_file}")
        return None

    with open(meta_file, 'r', encoding='utf-8') as f:
        meta = json.load(f)

    print(f"【加载】成功加载第{chapter_num}章元数据")
    return meta
```

**recover_from_history(work_dir, chapter_num)**
- 从 `character_state_history.json` 恢复人物状态
- 当 meta 文件读取失败时的容错机制

```python
def recover_from_history(work_dir, chapter_num):
    """
    从状态历史恢复人物状态
    """
    history_file = f"{work_dir}/character_state_history.json"

    if not os.path.exists(history_file):
        print(f"【错误】状态历史文件不存在：{history_file}")
        return None

    with open(history_file, 'r', encoding='utf-8') as f:
        history = json.load(f)

    chapter_key = f"chapter_{chapter_num}"
    if chapter_key not in history.get("states", {}):
        print(f"【错误】状态历史中无第{chapter_num}章记录")
        return None

    # 创建一个临时meta对象，只包含人物状态
    recovery_meta = {
        "character_state_tracker": history["states"][chapter_key]
    }

    print(f"【恢复】从状态历史恢复第{chapter_num}章人物状态")
    return recovery_meta
```

### 状态合并函数

**merge_state_tracker(current_state, update_state)**
- 合并人物状态更新到累积状态
- 用于逐章传递人物状态

```python
def merge_state_tracker(current_state, update_state):
    """
    合并人物状态更新
    """
    merged = deepcopy(current_state)

    for character_name, update in update_state.items():
        if character_name not in merged:
            merged[character_name] = {}

        # 合并 met_people（去重）
        if "met_people" in update:
            if "met_people" not in merged[character_name]:
                merged[character_name]["met_people"] = []
            for person in update["met_people"]:
                if person not in merged[character_name]["met_people"]:
                    merged[character_name]["met_people"].append(person)

        # 合并 relationships
        if "relationships" in update:
            if "relationships" not in merged[character_name]:
                merged[character_name]["relationships"] = {}
            for rel_name, rel_update in update["relationships"].items():
                if rel_name not in merged[character_name]["relationships"]:
                    merged[character_name]["relationships"][rel_name] = {}
                merged[character_name]["relationships"][rel_name] = rel_update

        # 合并 emotional_trajectory
        if "emotional_trajectory_addition" in update:
            if "emotional_trajectory" not in merged[character_name]:
                merged[character_name]["emotional_trajectory"] = {}
            merged[character_name]["emotional_trajectory"].update(
                update["emotional_trajectory_addition"]
            )

        # 更新 character_arc_stage
        if "character_arc_stage" in update:
            merged[character_name]["character_arc_stage"] = update["character_arc_stage"]

        # 合并 key_events_impact
        if "key_events_impact_addition" in update:
            if "key_events_impact" not in merged[character_name]:
                merged[character_name]["key_events_impact"] = []
            for event in update["key_events_impact_addition"]:
                merged[character_name]["key_events_impact"].append(event)

    return merged
```

## 【新增】完整写作流程函数

**write_chapter_with_verification(chapter_number, outline, previous_content, character_state_tracker, character_arc_mapping)**
- 完整的章节写作流程，包含所有验证检查点
- 确保9个步骤全部执行完成
- 每步都有验证机制

```python
def write_chapter_with_verification(chapter_number, outline, previous_content,
                                 character_state_tracker, character_arc_mapping):
    """
    完整的章节写作流程，包含所有验证检查点

    参数：
        chapter_number: 当前章节号
        outline: 完整大纲对象
        previous_content: 前文内容（第1章为空字符串）
        character_state_tracker: 当前人物状态
        character_arc_mapping: 人物弧光映射
    """

    work_dir = outline.get("work_dir", "./outputs/novel/work/")
    chapter_outline = outline["chapters"][chapter_number - 1]

    print(f"\n{'='*60}")
    print(f"【开始】第{chapter_number}章写作：{chapter_outline['title']}")
    print(f"{'='*60}\n")

    # ========== 步骤1：验证顺序执行 ==========
    print("【步骤1/10】验证章节执行顺序...")
    verify_sequential_execution(work_dir, chapter_number)

    # ========== 步骤2：调用 novelist-author（作家） ==========
    print(f"\n【步骤2/10】调用 novelist-author 写作初稿...")
    print(f"  章节标题：{chapter_outline['title']}")
    print(f"  人物状态：{list(character_state_tracker.keys())}")

    novelist_input = {
        "chapter_outline": chapter_outline,
        "character_arc": outline.get("character_arc", {}),
        "characters": outline.get("characters", {}),
        "character_state_tracker": character_state_tracker,
        "character_arc_mapping": character_arc_mapping,
        "previous_content": previous_content,
        "target_tone": outline.get("tone", "虐恋爽感"),
        "session_id": outline.get("session_id", ""),
        "creation_timestamp": outline.get("creation_timestamp", ""),
        "work_dir": work_dir
    }

    # 【验证】调用前检查输入资源
    validate_all_inputs(
        chapter_number=chapter_number,
        chapter_outline=chapter_outline,
        character_state_tracker=character_state_tracker,
        character_arc_mapping=character_arc_mapping,
        previous_content=previous_content,
        work_dir=work_dir
    )

    novelist_output = Skill("novelist-author", args=json.dumps(novelist_input))

    # 【验证】作家输出
    verify_skill_completion(
        "novelist-author",
        novelist_output,
        ["draft_content", "draft_word_count", "character_state_update"]
    )

    print(f"  ✓ 初稿字数：{novelist_output['draft_word_count']}")

    # ========== 步骤3：调用 word-count-controller（字数控制） ==========
    print(f"\n【步骤3/10】调用 word-count-controller 调整字数...")
    target_min = outline.get("chapter_word_min", 800)
    target_max = outline.get("chapter_word_max", 1000)

    wordcount_input = {
        "content": novelist_output["draft_content"],
        "target_min": target_min,
        "target_max": target_max,
        "current_words": novelist_output["draft_word_count"],
        "work_dir": work_dir
    }

    wordcount_output = Skill("word-count-controller", args=json.dumps(wordcount_input))

    # 【验证】字数控制输出
    verify_skill_completion(
        "word-count-controller",
        wordcount_output,
        ["adjusted_content", "actual_words"]
    )

    # 【验证】字数是否在范围内
    actual_words = wordcount_output["actual_words"]
    if actual_words < target_min * 0.9 or actual_words > target_max * 1.1:
        raise Error(f"字数超出范围：{actual_words}，目标{target_min}-{target_max}")

    print(f"  ✓ 调整后字数：{actual_words}")

    # ========== 步骤4：调用 hook-crafter（钩子师） ==========
    print(f"\n【步骤4/10】调用 hook-crafter 设计章末钩子...")

    # 获取下一章大纲（最后一章除外）
    next_chapter = None
    if chapter_number < outline["total_chapters"]:
        next_chapter = outline["chapters"][chapter_number]

    hookcrafter_input = {
        "chapter_content": wordcount_output["adjusted_content"],
        "chapter_number": chapter_number,
        "current_hook_type": chapter_outline.get("ending_hook_type", "悬念"),
        "next_chapter_outline": next_chapter,
        "target_tone": outline.get("tone", "虐恋爽感"),
        "work_dir": work_dir
    }

    hookcrafter_output = Skill("hook-crafter", args=json.dumps(hookcrafter_input))

    # 【验证】钩子师输出
    verify_skill_completion(
        "hook-crafter",
        hookcrafter_output,
        ["hook_text", "hook_type", "final_content"]
    )

    print(f"  ✓ 钩子类型：{hookcrafter_output['hook_type']}")
    print(f"  ✓ 钩子内容：{hookcrafter_output['hook_text']}")

    # ========== 步骤5：调用 character-consistency-checker（人物一致性检查） ==========
    print(f"\n【步骤5/10】调用 character-consistency-checker 检查人物一致性...")

    consistency_input = {
        "chapter_number": chapter_number,
        "chapter_content": hookcrafter_output["final_content"],
        "previous_content": previous_content,
        "character_state_tracker": character_state_tracker,
        "character_arc_mapping": character_arc_mapping,
        "session_id": outline.get("session_id", ""),
        "work_dir": work_dir
    }

    consistency_output = Skill("character-consistency-checker", args=json.dumps(consistency_input))

    # 【验证】一致性检查输出
    verify_skill_completion(
        "character-consistency-checker",
        consistency_output,
        ["status", "score", "character_state_update"]
    )

    # 【验证】检查是否通过
    if consistency_output["status"] == "needs_revision":
        print(f"  ⚠ 人物一致性检查未通过，问题：{consistency_output.get('issues', [])}")
        print(f"  ⚠ 建议：{consistency_output.get('suggestions', [])}")
        # 根据建议修改后重新检查（此处简化处理）

    print(f"  ✓ 一致性评分：{consistency_output['score']}")

    # ========== 步骤6：保存章节文件 ==========
    print(f"\n【步骤6/10】保存章节文件...")

    chapter_file = f"{work_dir}/temp_chapter_{chapter_number}.md"
    with open(chapter_file, 'w', encoding='utf-8') as f:
        f.write(hookcrafter_output["final_content"])

    print(f"  ✓ 文件已保存：{chapter_file}")

    # ========== 步骤7：保存 meta 文件 ==========
    print(f"\n【步骤7/10】保存章节meta文件...")

    # 合并人物状态更新
    updated_state_tracker = merge_state_tracker(
        character_state_tracker,
        consistency_output["character_state_update"]
    )

    result = {
        "chapter_number": chapter_number,
        "chapter_title": chapter_outline["title"],
        "chapter_content": hookcrafter_output["final_content"],
        "word_count": actual_words,
        "ending_hook": hookcrafter_output["hook_text"],
        "hook_type": hookcrafter_output["hook_type"],
        "character_state_tracker": updated_state_tracker,
        "character_state_update": consistency_output["character_state_update"],
        "meta": {
            "author_draft_words": novelist_output["draft_word_count"],
            "hook_crafted": True,
            "word_adjusted": True,
            "consistency_score": consistency_output["score"]
        }
    }

    meta = save_chapter_meta(work_dir, chapter_number, result)

    # ========== 步骤8：调用 editor-reviewer（编辑审核） ==========
    print(f"\n【步骤8/10】调用 editor-reviewer 编辑审核...")

    editor_input = {
        "content_type": "chapter",
        "chapter_number": chapter_number,
        "chapter_content": hookcrafter_output["final_content"],
        "chapter_outline": chapter_outline,
        "target_words": target_min,
        "work_dir": work_dir
    }

    editor_output = Skill("editor-reviewer", args=json.dumps(editor_input))

    # 【验证】编辑审核输出
    verify_skill_completion(
        "editor-reviewer",
        editor_output,
        ["status", "score"]
    )

    # 【验证】审核是否通过
    if editor_output["status"] != "pass":
        raise Error(f"编辑审核未通过：{editor_output.get('issues', [])}")

    print(f"  ✓ 编辑评分：{editor_output['score']}")

    # ========== 步骤9：调用 reader-feedback（读者反馈） ==========
    print(f"\n【步骤9/10】调用 reader-feedback 读者反馈...")

    # 提取目标读者群体
    category = outline.get("category", "")
    if category.startswith("女频-"):
        target_audience = "female"
    elif category.startswith("男频-"):
        target_audience = "male"
    else:
        target_audience = "all"

    reader_input = {
        "feedback_type": "chapter",
        "chapter_number": chapter_number,
        "chapter_content": hookcrafter_output["final_content"],
        "target_audience": target_audience,
        "target_tone": outline.get("tone", "虐恋爽感"),
        "work_dir": work_dir
    }

    reader_output = Skill("reader-feedback", args=json.dumps(reader_input))

    # 【验证】读者反馈输出
    verify_skill_completion(
        "reader-feedback",
        reader_output,
        ["score"]
    )

    # 【验证】评分是否达标
    if reader_output["score"] < 8.0:
        print(f"  ⚠ 读者评分偏低：{reader_output['score']}，建议：{reader_output.get('low_points', [])}")

    print(f"  ✓ 读者评分：{reader_output['score']}")

    # ========== 步骤10：更新状态历史 ==========
    print(f"\n【步骤10/10】更新状态历史备份...")

    backup_state_history(work_dir, chapter_number, updated_state_tracker)

    # ========== 最终验证 ==========
    print(f"\n{'='*60}")
    print(f"【验证】执行完整检查点...")
    print(f"{'='*60}\n")

    verify_chapter_completion(work_dir, chapter_number, outline)

    print(f"\n{'='*60}")
    print(f"【完成】第{chapter_number}章写作完成！")
    print(f"{'='*60}\n")

    return result
```

## 【新增】主执行流程

**execute_chapter_writing_loop(outline)**
- 章节写手的主执行函数
- 循环调用 `write_chapter_with_verification` 完成所有章节
- 严格顺序执行，每章完成后才进入下一章

```python
def execute_chapter_writing_loop(outline):
    """
    章节写手主执行流程
    """
    total_chapters = outline["total_chapters"]
    work_dir = outline.get("work_dir", "./outputs/novel/work/")
    character_arc_mapping = outline.get("character_arc_mapping", {})

    print(f"\n{'#'*60}")
    print(f"# 章节写手开始执行")
    print(f"# 总章节数：{total_chapters}")
    print(f"# 字数范围：{outline.get('chapter_word_min', 800)}-{outline.get('chapter_word_max', 1000)}")
    print(f"{'#'*60}\n")

    # 初始化状态历史
    init_state_history(work_dir)

    # 初始化人物状态（第1章使用初始状态）
    character_state_tracker = outline.get("character_initial_state", {})
    previous_content = ""  # 第1章没有前文

    # 循环写章节
    for chapter_number in range(1, total_chapters + 1):

        # 写入当前章节
        result = write_chapter_with_verification(
            chapter_number=chapter_number,
            outline=outline,
            previous_content=previous_content,
            character_state_tracker=character_state_tracker,
            character_arc_mapping=character_arc_mapping
        )

        # 保存结果到内存列表（供后续步骤使用）
        # ...

        # 更新状态供下一章使用
        character_state_tracker = result["character_state_tracker"]

        # 加载前文内容供下一章使用
        if chapter_number < total_chapters:
            previous_content = load_previous_chapters(work_dir, chapter_number + 1)

    print(f"\n{'#'*60}")
    print(f"# 章节写手执行完成")
    print(f"# 已完成章节数：{total_chapters}")
    print(f"{'#'*60}\n")

    return {"status": "completed", "total_chapters": total_chapters}
```
