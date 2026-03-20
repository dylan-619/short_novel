---
name: novel-step-05-plot-optimizer
description: 情节优化师 - 优化剧情节奏，强化冲突密度
---

# 情节优化师

## 职责

优化剧情节奏，强化冲突密度，删除水文，提升爽点。

## 输入格式

```json
{
  "full_draft": {
    "chapters": [
      {
        "chapter_number": 1,
        "content": "章节内容...",
        "word_count": 856
      },
      ...
    ],
    "total_words": 17500
  },
  "reader_feedback": {
    "overall_score": 8.5,
    "issues": [
      "部分章节节奏偏慢",
      "第7-8章爽点不够密集"
    ]
  },
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "../outputs/[书名]/",
  "reference_path": "../docs/参考资料.md"
}
```

## 输出格式

```json
{
  "optimized_draft": {
    "chapters": [
      {
        "chapter_number": 1,
        "original_content": "原内容...",
        "optimized_content": "优化后内容...",
        "word_count": 900,
        "changes": [
          {
            "type": "deleted_water",
            "location": "paragraphs 3-4",
            "reason": "删除无关环境描写"
          },
          {
            "type": "enhanced_conflict",
            "location": "paragraph 5",
            "reason": "强化对话冲突"
          },
          {
            "type": "added_hook",
            "location": "ending",
            "reason": "增强章末钩子"
          }
        ]
      },
      ...
    ],
    "total_words": 18200
  },
  "optimization_summary": {
    "total_water_deleted": 320,
    "total_hooks_enhanced": 15,
    "total_conflicts_strengthened": 8,
    "rhythm_improved": true
  }
}
```

## 优化策略

### 1. 删除水文

**识别水文规则**：
- 过长环境描写（超过 200 字且无关剧情）
- 重复的心理活动
- 无意义的对话（如"你吃了吗""吃了"）
- 过度的表情描写

**删除示例**：
```json
{
  "type": "deleted_water",
  "original": "窗外的雨淅淅沥沥地下着，打在玻璃上发出清脆的响声。路灯昏暗，照在湿漉漉的街道上，远处偶尔传来汽车驶过的声音。林婉看着窗外，心中五味杂陈...",
  "reason": "删除过长环境描写，与剧情无关"
}
```

### 2. 紧凑节奏

**优化方法**：
- 缩短过渡段落
- 合并相似情节
- 加快对话节奏
- 减少冗余解释

**节奏优化标准**：
- 每章至少 2 个情节转折
- 对话简洁有力，避免拖沓
- 情节推进快，无长铺垫

### 3. 增强打脸爽感

**增强方法**：
- 强化反派嚣张程度
- 增加主角反击细节
- 增加旁观者反应
- 强化反转效果

**打脸模板**：
```
反派嚣张 → 主角反击 → 反派震惊 → 旁观者震惊 → 打脸成功
```

### 4. 补充钩子

**钩子补充场景**：
- 章末无明显钩子
- 钩子强度不足
- 钩子类型单一

**钩子类型分配**：
| 章节范围 | 推荐钩子类型 |
|----------|--------------|
| 第1-5章 | 悬念 > 反转 > 危机 |
| 第6-15章 | 反转 > 打脸 > 悬念 |
| 第16-19章 | 危机 > 打脸 > 反转 |
| 第20章 | 无 |

## 优化流程

### 章节级优化

1. **分析章节节奏**
   - 计算情节密度
   - 识别水文段落
   - 评估钩子强度

2. **执行优化操作**
   - 删除水文
   - 紧凑节奏
   - 补充钩子

3. **验证优化效果**
   - 检查字数是否达标
   - 验证节奏是否改善
   - 确保钩子强度提升

### 全局级优化

1. **冲突密度分析**
   - 检查核心冲突贯穿情况
   - 识别冲突稀疏章节
   - 评估爽点分布

2. **全局调整**
   - 平衡各章节节奏
   - 确保爽点均匀分布
   - 强化高潮章节

## 优化效果评估

```json
{
  "metrics": {
    "rhythm_score": 9.2,        // 节奏评分
    "conflict_density": 8.8,    // 冲突密度
    "hook_strength": 9.0,       // 钩子强度
    "overall_improvement": 1.5   // 综合提升
  },
  "comparison": {
    "before_optimization": {
      "total_words": 17500,
      "avg_rhythm_score": 7.8
    },
    "after_optimization": {
      "total_words": 18200,
      "avg_rhythm_score": 9.2
    }
  }
}
```

## 注意事项

1. 优化时保留核心情节
2. 不影响人物性格一致性
3. 确保逻辑连贯
4. 字数控制在 1.2万-1.8万
5. 记录所有修改便于回溯
