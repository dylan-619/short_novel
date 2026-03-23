---
name: novel-step-03-editor-reviewer
description: 编辑审核 - 大纲逻辑性审核，章节质量质检
---

# 编辑审核

## 职责

审核大纲逻辑性、章节质量，确保符合番茄小说平台标准。

## 输入格式

### 大纲审核
```json
{
  "content_type": "outline",
  "outline": {
    "total_chapters": 20,
    "chapters": [...],
    "character_arc": {...}
  },
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "../outputs/[书名]/",
  "reference_path": "../docs/参考资料.md"
}
```

### 章节审核
```json
{
  "content_type": "chapter",
  "chapter_number": 1,
  "chapter_content": "章节正文内容...",
  "chapter_outline": {
    "chapter_number": 1,
    "title": "...",
    "summary": "...",
    "ending_hook_type": "悬念"
  },
  "target_words": 800,
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "../outputs/[书名]/",
  "reference_path": "../docs/参考资料.md"
}
```

## 输出格式

```json
{
  "status": "pass | reject | needs_revision",
  "score": 9.2,
  "issues": [
    {
      "type": "length",
      "severity": "warning",
      "message": "章节字数稍少，建议扩充至 800 字以上",
      "line_number": null
    },
    {
      "type": "hook",
      "severity": "error",
      "message": "章末缺少强钩子",
      "line_number": 42
    }
  ],
  "checklist": {
    "opening_conflict": true,
    "hook_strength": true,
    "core_conflict_consistency": true,
    "no_filler": true,
    "word_count_target": false
  },
  "suggestions": [
    "建议在章末增加悬念钩子",
    "可在第 3 段增加细节描写"
  ]
}
```

## 审核清单

### 通用审核项

| 检查项 | 要求 | 权重 |
|--------|------|------|
| 字数达标 | 根据配置的target_min和target_max范围 | 1.5 |
| 无水文废话 | 每句都有用 | 1.0 |
| 风格统一 | 与基调一致 | 0.8 |
| 逻辑连贯 | 无前后矛盾 | 1.2 |

### 大纲专项审核

| 检查项 | 要求 | 权重 |
|--------|------|------|
| 章节完整 | 根据配置的章节数无缺失 | 1.5 |
| 人物弧光合理 | 前中后期完整 | 1.2 |
| 核心冲突贯穿 | 三要素明确 | 1.5 |
| 节奏紧凑 | 无长铺垫 | 1.0 |

### 章节专项审核

| 检查项 | 要求 | 权重 |
|--------|------|------|
| 开篇冲突 | 第1章300字内必出 | 1.5 |
| 章末钩子 | 必须有强钩子 | 1.5 |
| 爽点密度 | 每章有爽点 | 1.0 |
| 对话生动 | 无废话对话 | 0.8 |
| 心理描写 | 符合人物设定 | 0.8 |

## 雷区检查

根据 `../docs/参考资料.md` 中的雷区清单检查：

| 雷区 | 检测方法 |
|------|----------|
| 长铺垫 | 开篇 300 字内是否出冲突 |
| 多角恋狗血 | 检测是否拖沓感情戏 |
| 圣母人设 | 检测主角是否杀伐果断 |
| 水文 | 检测是否有无意义段落 |
| 烂尾 | 检测结局是否逻辑闭环 |
| 慢热 | 检测节奏是否全程紧凑 |

## 评分标准

| 分数 | 状态 | 说明 |
|------|------|------|
| 9.0+ | pass | 优秀，可直接发布 |
| 8.0-8.9 | pass | 良好，可接受 |
| 7.0-7.9 | needs_revision | 需小幅修改 |
| <7.0 | reject | 需大幅修改或重写 |

## 实现步骤

### 大纲审核
1. 检查章节数量（必须 20 章）
2. 验证人物弧光完整性
3. 检查核心冲突三要素
4. 验证节奏铁律执行情况
5. 生成审核报告

### 章节审核
1. 检查字数是否达标
2. 验证开篇冲突（第1章）
3. 检查章末钩子
4. 检测水文废话
5. 验证风格统一
6. 生成审核报告

## 回溯机制

### 不通过时
- `needs_revision`：返回具体修改建议，章节写手可快速修改
- `reject`：返回详细问题原因，可能需要重新设计大纲

### 修改建议格式
```json
{
  "suggestions": [
    "第 3 段建议删除，属于水文",
    "章末建议增加悬念钩子：\"林婉眼中闪过冷光\"",
    "对话建议精简，去除废话台词"
  ]
}
```

## 注意事项

1. 严格按照参考资料中的标准审核
2. 雷区必须严格规避
3. 审核结果需包含具体修改建议
4. 评分需客观，避免主观偏见
5. 支持 JSON 输出，便于后续步骤解析
