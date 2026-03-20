---
name: novel-generator-main
description: 小说生成主入口 - 自动化完成20章短篇番茄小说生成流程
---

# 小说生成主入口

## 职责

协调整个小说生成流程，从选题到最终输出，自动化完成20章短篇番茄小说的生成。

## 输入格式

```json
{
  "user_preference": "female | male | all",
  "specific_genre": "(可选) 具体题材，如：追妻火葬场、闪婚、重生复仇、年代文、赘婿逆袭、规则怪谈等",
  "custom_topic": "(可选) 自定义题材或故事核",
  "output_dir": "../outputs",
  "work_dir": "../outputs/[书名]/",
  "reference_path": "../docs/参考资料.md"
}
```

### 输入参数说明

| 参数 | 类型 | 忌填 | 说明 | 可选值 |
|------|------|--------|------|--------|
| user_preference | string | 否 | 目标读者群体 | female(女频), male(男频), all(全频) |
| specific_genre | string | 是 | 具体题材 | 追妻火葬场、/闪婚、重生复仇、年代文、赘婿逆袭、规则怪谈等 |
| custom_topic | string | 是 | 自定义题材或故事核 | 任意字符串 |
| output_dir | string | 否 | 输出目录路径 | 默认 "../outputs" |
| reference_path | string | 否 | 参考资料路径 | 默认 "../docs/参考资料.md" |

## 输出格式

```json
{
  "status": "completed",
  "novel_info": {
    "title": "ICU 门外，他抱白月光笑时，我重生了",
    "category": "女频-追妻火葬场",
    "tone": "虐恋爽感",
    "word_count": 18200,
    "chapter_count": 20
  },
  "generation_log": {
    "step_1_topic_selector": {
      "status": "completed",
      "output": {...},
      "timestamp": "2026-03-18T19:30:00Z"
    },
    "step_2_outline_designer": {
      "status": "completed",
      "output": {...},
      "timestamp": "2026-03-18T19:32:00Z"
    },
    "step_3_editor_reviewer": {
      "status": "completed",
      "output": {...},
      "timestamp": "2026-03-18T19:34:00Z"
    },
    "step_4_chapter_writer": {
      "status": "completed",
      "chapters_generated": 20,
      "output": [...],
      "timestamp": "2026-03-18T19:45:00Z"
    },
    "step_5_plot_optimizer": {
      "status": "completed",
      "output": {...},
      "timestamp": "2026-03-18T19:47:00Z"
    },
    "step_6_tone_consistency": {
      "status": "completed",
      "output": {...},
      "timestamp": "2026-03-18T19:48:00Z"
    },
    "step_7_conflict_checker": {
      "status": "completed",
      "output": {...},
      "timestamp": "2026-03-18T19:49:00Z"
    },
    "step_8_final_compiler": {
      "status": "completed",
      "output": {
        "novel_file": "../outputs/ICU 门外，他抱白月光笑时，我重生了.md",
        "meta_file": "../outputs/ICU 门外，他抱白月光笑时，我重生了_meta.json"
      },
      "timestamp": "2026-03-18T19:50:00Z"
    }
  },
  "quality_reports": {
    "final_quality_score": 9.33,
    "plot_optimizer": {
      "rhythm_score": 9.2,
      "conflict_density": 8.8,
      "hook_strength": 9.0
    },
    "tone_consistency": {
      "overall_score": 9.5,
      "consistency_level": "high"
    },
    "conflict_checker": {
      "overall_score": 9.3,
      "core_conflict_mapping": {
        "early_stage": true,
        "mid_stage": true,
        "late_stage": true
      }
    }
  },
  "output_files": {
    "novel_file": "../outputs/ICU 门外，他抱白月光笑时，我重生了.md",
    "meta_file": "../outputs/ICU 门外，他抱白月光笑时，我重生了_meta.json"
  }
}
```

## 流程执行

### 完整执行流程

```
用户启动
    ↓
步骤1: 选题师 (novel-step-01-topic-selector)
    ↓
步骤2: 大纲师 (novel-step-02-outline-designer)
    ↓
步骤3: 编辑审核 (novel-step-03-editor-reviewer)
    ↓
步骤4: 章节写手 (novel-step-04-chapter-writer)
    ├→ 作家 (novel-sub-skill-novelist-author) × 20
    ├→ 字数控制 (novel-sub-skill-word-count-controller) × 20
    ├→ 钩子师 (novel-sub-skill-hook-crafter) × 20
    ├→ 编辑审核 (novel-step-03-editor-reviewer) × 20
    └→ 读者反馈 (novel-sub-skill-reader-feedback) × 20
    ↓
步骤5: 情节优化师 (novel-step-05-plot-optimizer)
    ↓
步骤6: 基调维持 (novel-step-06-tone-consistency)
    ↓
步骤7: 冲突检查 (novel-step-07-conflict-checker)
    ↓
步骤8: 最终汇编 (novel-step-08-final-compiler)
    ↓
输出完整小说
```

## 执行步骤详解

### 步骤1: 选题师

**调用技能**: `/novel-step-01-topic-selector`

**输入**:
```json
{
  "user_preference": "female | male | all",
  "specific_genre": "(可选) 如：闪婚、重生复仇、年代文等",
  "reference_path": "../docs/参考资料.md"
}
```

**输出**: 全新题材、书名、核心爽点、人物关系、基调
**注意**: 根据参考资料中的创作规律（书名公式、节奏铁律、核心冲突三要素等）创作全新主题，而非选择已有模板

**预期时间**: 1-2分钟

---

### 步骤2: 大纲师

**调用技能**: `/novel-step-02-outline-designer`

**输入**: 步骤1的选题输出

**输出**: 20章完整大纲、人物弧光、时间线分配

**预期时间**: 3-5分钟

---

### 步骤3: 大纲审核

**调用技能**: `/novel-step-03-editor-reviewer`

**输入**: 步骤2的大纲输出

**输出**: 大纲审核报告

**预期时间**: 1-2分钟

**审核不通过处理**: 返回步骤2重新设计

---

### 步骤4: 章节写手

**调用技能**: `/novel-step-04-chapter-writer`

**循环**: 20次（每次生成一章）

**内部流程**:
1. 作家写作初稿 (`/novelist-author`)
2. 钩子设计 (`/hook-crafter`) - 添加50-100字钩子
3. 字数控制 (`/word-count-controller`) - 确保总字数800-1000
4. 编辑审核 (`/novel-step-03-editor-reviewer`)
5. 读者反馈 (`/reader-feedback`)

**预期时间**: 每章1-2分钟，总计20-40分钟

**审核不通过处理**: 重新写作该章（最多重试3次）

---

### 步骤5: 情节优化师

**调用技能**: `/novel-step-05-plot-optimizer`

**输入**: 步骤4的完整章节输出

**输出**: 优化后的全文、优化摘要

**预期时间**: 3-5分钟

---

### 步骤6: 基调维持

**调用技能**: `/novel-step-06-tone-consistency`

**输入**: 步骤5的优化后全文

**输出**: 基调一致性报告

**预期时间**: 2-3分钟

---

### 步骤7: 冲突检查

**调用技能**: `/novel-step-07-conflict-checker`

**输入**: 步骤6的基调统一后全文

**输出**: 冲突完整性报告

**预期时间**: 2-3分钟

---

### 步骤8: 最终汇编

**调用技能**: `/novel-step-08-final-compiler`

**输入**: 所有步骤的输出汇总

**输出**:
- 完整小说文件 (`../outputs/[书名]/[书名].md`)
- 元数据文件 (`../outputs/[书名]/[书名]_meta.json`)
- 章节文件目录 (`../outputs/[书名]/chapters/`)
- 工作目录 (`../outputs/[书名]/work/`)

**预期时间**: 1-2分钟

## 数据流转

### 全局数据结构

```json
{
  "session_id": "uuid",
  "timestamp": "2026-03-18T19:30:00Z",
  "input": {
    "user_preference": "female",
    "custom_topic": null,
    "output_dir": "../outputs",
    "reference_path": "../docs/参考资料.md"
  },
  "state": {
    "current_step": 1,
    "max_retries": 3,
    "retry_count": 0
  },
  "data": {
    "topic": null,
    "outline": null,
    "chapters": [],
    "optimized_draft": null,
    "consistency_report": null,
    "conflict_report": null
  },
  "output": null
}
```

### 数据传递机制

每个步骤接收上一步骤的完整输出，确保数据完整性和可追溯性。

## 错误处理

### 重试机制

**编辑审核不通过**:
- 章节级: 最多重试3次
- 大纲级: 返回步骤2重新设计

**字数不达标**:
- 调用字数控制技能调整
- 最多调整10次

**技能调用失败**:
- 记录错误日志
- 使用降级策略
- 通知用户

### 回溯机制

**大纲审核不通过**:
- 返回步骤2 (大纲师)
- 重新设计20章大纲

**章节审核不通过**:
- 重新生成该章节
- 不影响其他章节

## 进度输出

### 进度格式

```
[步骤1/8] 选题师: ✓ 完成 (耗时: 1.5分钟)
[步骤2/8] 大纲师: ⏳ 进行中...
[步骤3/8] 编辑审核: ⏸️ 等待中
[步骤4/8] 章节写手: ⏸️ 等待中
[步骤5/8] 情节优化师: ⏸️ 等待中
[步骤6/8] 基调维持: ⏸️ 等待中
[步骤7/8] 冲突检查: ⏸️ 等待中
[步骤8/8] 最终汇编: ⏸️ 等待中

总体进度: 12.5% (1/8 步骤完成)
预计剩余时间: 40分钟
```

### 章节进度

```
[步骤4/8] 章节写手:
  章节1: ✓ 完成
  章节2: ✓ 完成
  章节3: ⏳ 进行中...
  章节4-20: ⏸️ 等待中

章节进度: 15% (3/20 章节完成)
```

## 使用示例

### 示例1: 生成女频追妻火葬场小说

**输入**:
```json
{
  "user_preference": "female",
  "output_dir": "../outputs"
}
```

**预期输出**:
- 题材: 基于参考资料创作规律的全新主题
- 书名: 符合爆款公式 + 创新元素
- 字数: 1.2万-1.8万字
- 章节: 20章
- 基调: 符合主题风格

---

### 示例2: 生成男频赘婿逆袭小说

**输入**:
```json
{
  "user_preference": "male",
  "output_dir": "../outputs"
}
```

**预期输出**:
- 题材: 男频-都市逆袭
- 书名: 符合爆款公式
- 字数: 1.2万-1.8万字
- 章节: 20章
- 基调: 打脸爽文

---

### 示例3: 生成全频脑洞小说

**输入**:
```json
{
  "user_preference": "all",
  "custom_topic": "摸鱼系统",
  "output_dir": "../outputs"
}
```

**预期输出**:
- 题材: 全频-脑洞
- 书名: 包含"摸鱼系统"
- 字数: 1.2万-1.8万字
- 章节: 20章
- 基调: 搞笑脑洞

## 注意事项

1. **输入验证**: 确保所有必填参数已提供
2. **错误处理**: 每个步骤都有完善的错误处理机制
3. **回溯机制**: 审核不通过时能够回溯修改
4. **进度输出**: 实时显示生成进度
5. **日志记录**: 记录所有步骤的详细日志
6. **数据完整性**: 确保数据在步骤间完整传递
7. **质量控制**: 每个步骤都有质量检查
8. **输出验证**: 最终输出文件完整性验证

## 快速开始

### 最简单的使用方式

```
/novel-generator-main
```

系统会自动:
1. 询问用户偏好（女频/男频/全频）
2. 按照流程自动生成小说
3. 输出到 `../outputs/` 目录
4. 显示生成进度和质量报告

### 高级使用方式

```json
{
  "user_preference": "female",
  "custom_topic": "重生复仇",
  "output_dir": "../outputs"
}
```

自定义题材或故事核，生成特定主题的小说。
