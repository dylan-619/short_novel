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
    "character_arc": {...}
  },
  "previous_chapters": [],  // 已写章节
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "../outputs/[书名]/work/",
  "reference_path": "../docs/参考资料.md"
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
4. **novel-step-03-editor-reviewer** → 质检
5. **novel-sub-skill-reader-feedback** → 读者反馈

## target_audience 提取逻辑

从 `outline.category` 中提取目标读者群体：
- `category` 以 "女频-" 开头 → `target_audience: "female"`
- `category` 以 "男频-" 开头 → `target_audience: "male"`
- `category` 以 "全频-" 开头 → `target_audience: "all"`

## 子技能调用说明

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
  "previous_content": "前文内容...",
  "target_tone": "虐恋爽感",
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "../outputs/[书名]/work/"
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

### 4. editor-reviewer（编辑审核）

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

### 5. reader-feedback（读者反馈）

**【重要】** 临时文件输出到 `work_dir` 目录

输入：
```json
{
  "content": "完整章节内容...",
  "target_audience": "female | male | all",  // 从category提取
  "target_tone": "虐恋爽感",
  "chapter_number": 1,
  "work_dir": "../outputs/[书名]/work/"
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

## 循环执行

章节写手需要循环执行根据配置的章节数，每次生成一章：

```
total_chapters = outline.total_chapters  # 从配置读取，默认20
for chapter in 1..total_chapters:
    result = chapter_writer(chapter, outline, previous_chapters)
    previous_chapters.append(result)
    if chapter == total_chapters:
        break
```

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
