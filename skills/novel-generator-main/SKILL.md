---
name: novel-generator-main
description: 小说生成主入口 - 自动化完成小说生成流程，支持灵活的章节数和字数配置
---

# 小说生成主入口

## 职责

协调整个小说生成流程，从选题到最终输出，自动化完成小说的生成。支持灵活的章节数和字数配置，适用于不同场景：
- **番茄小说**：20章，800-1000字/章
- **短视频剧本**：5-10章，200-300字/章
- **中篇小说**：30-50章，1000-1500字/章
- **长篇小说**：100+章，1500-3000字/章

## 输入格式

```json
{
  "user_preference": "female | male | all",
  "specific_genre": "(可选) 具体题材，如：追妻火葬场、闪婚、重生复仇、年代文、赘婿逆袭、规则怪谈等",
  "custom_topic": "(可选) 自定义题材或故事核",
  "output_dir": "./outputs",
  "work_dir": "./outputs/[书名]/",
  "reference_path": "./docs/参考资料.md",
  "config": {
    "target_chapters": 20,
    "chapter_word_min": 800,
    "chapter_word_max": 1000,
    "target_total_words": null
  }
}
```

### 输入参数说明

**【重要】选题逻辑 - 必须严格遵守以下优先级：**

| 优先级 | 参数 | 说明 | 处理方式 |
|--------|------|------|----------|
| 1 | custom_topic | 用户提供的具体故事核/题材 | 直接使用，创作完整故事 |
| 2 | specific_genre | 用户指定的具体题材类型 | 在该题材内创作全新故事 |
| 3 | user_preference | 仅指定读者群体 | 系统自动生成全新主题 |

**【关键】输出路径规则：**
- 所有临时文件必须输出到 `outputs/[书名]/work/` 目录下
- 禁止将临时文件直接输出到 `outputs/` 根目录
- 临时文件包括：temp_topic.json, temp_outline.json, temp_chapter_*.md, temp_*.json 等

| 参数 | 类型 | 必填 | 说明 | 可选值/默认值 |
|------|------|--------|------|--------|
| user_preference | string | 否 | 目标读者群体 | female(女频), male(男频), all(全频)，默认all |
| specific_genre | string | 否 | 具体题材 | 追妻火葬场、闪婚、重生复仇、年代文、赘婿逆袭、规则怪谈等，可选 |
| custom_topic | string | 否 | 自定义题材或故事核 | 任意字符串，可选 |
| output_dir | string | 否 | 输出目录路径 | 默认 "./outputs" |
| reference_path | string | 否 | 参考资料路径 | 默认 "./docs/参考资料.md" |
| config | object | 否 | 生成配置参数 | 见下方配置说明 |

### config 配置说明

| 参数 | 类型 | 忌填 | 说明 | 默认值 |
|------|------|--------|------|--------|
| target_chapters | number | 否 | 目标章节数 | 20 |
| chapter_word_min | number | 否 | 每章最少字数 | 800 |
| chapter_word_max | number | 否 | 每章最多字数 | 1000 |
| target_total_words | number | 否 | 目标总字数（可选，优先级低于章节数） | null |

**注意**：
- 如果指定 `target_total_words`，系统会自动计算合适的章节数
- 章节数 = 目标总字数 ÷ 平均章节数字数（向上取整）
- 如果 `target_chapters` 和 `target_total_words` 同时指定，以 `target_chapters` 优先

## 输出格式

```json
{
  "status": "completed",
  "novel_info": {
    "title": "ICU 门外，他抱白月光笑时，我重生了",
    "category": "女频-追妻火葬场",
    "tone": "虐恋爽感",
    "word_count": 18200,
    "chapter_count": 20,
    "work_dir": "./outputs/ICU 门外，他抱白月光笑时，我重生了/"
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
        "novel_file": "./outputs/ICU 门外，他抱白月光笑时，我重生了.md",
        "meta_file": "./outputs/ICU 门外，他抱白月光笑时，我重生了_meta.json"
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
    "novel_file": "./outputs/ICU 门外，他抱白月光笑时，我重生了/ICU 门外，他抱白月光笑时，我重生了.md",
    "meta_file": "./outputs/ICU 门外，他抱白月光笑时，我重生了/ICU 门外，他抱白月光笑时，我重生了_meta.json",
    "work_dir": "./outputs/ICU 门外，他抱白月光笑时，我重生了/work/"
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
     ├→ 生成 character_initial_state
     ├→ 生成 character_arc_mapping
     └→ 生成 outline.json
     ↓
 步骤3: 编辑审核 (novel-step-03-editor-reviewer)
     ↓
 步骤4: 章节写手 (novel-step-04-chapter-writer)
     ├→ 作家 (novel-sub-skill-novelist-author) × N章
     ├→ 字数控制 (novel-sub-skill-word-count-controller) × N章
     ├→ 钩子师 (novel-sub-skill-hook-crafter) × N章
     ├→ 人物一致性检查 (novel-sub-skill-character-consistency-checker) × N章 【新增】
     ├→ 编辑审核 (novel-step-03-editor-reviewer) × N章
     ├→ 读者反馈 (novel-sub-skill-reader-feedback) × N章
     └→ 状态持久化 + 备份 × N章 【新增】
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
  "reference_path": "./docs/参考资料.md"
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

**【绝对禁止并行】**：
- ❌ 禁止使用 subagent/parallel agents 同时写多章
- ❌ 禁止一次生成多章内容
- ✅ **必须严格按顺序**：第1章完成后才能写第2章

**循环**: 根据配置的章节数（默认20次），**必须顺序执行**

**【关键】人物状态追踪**:
- 第1章：使用 `outline.character_initial_state` 作为初始状态
- 每章写完后，从作家输出中提取 `character_state_update`
- **必须合并**到累积的 `character_state_tracker` 中
- 下一章写作时**必须传入**更新后的状态
- **【重要】必须持久化存储**：
  - 每章输出 `temp_chapter_XX_meta.json` 必须包含 `character_state_tracker`
  - 下一章读取时，从上一章的 meta 文件加载状态

**内部流程**（每章必须按顺序执行）:
 1. 作家写作初稿 (`/novelist-author`) - 传入 `character_state_tracker` 和 `character_arc_mapping`
 2. 钩子设计 (`/hook-crafter`) - 添加50-100字钩子
 3. 字数控制 (`/word-count-controller`) - 确保总字数在配置范围内（默认800-1000）
 4. 人物一致性检查 (`/character-consistency-checker`) - 检查人物行为、情感、关系【新增】
 5. 编辑审核 (`/novel-step-03-editor-reviewer`)
 6. 读者反馈 (`/reader-feedback`)
 7. **【关键】状态持久化**：
    - 保存 `character_state_tracker` 到 `temp_chapter_XX_meta.json`
    - 备份到 `character_state_history.json`【新增】
    - 更新 `character_state_tracker` 后才能写下一章

**预期时间**: 每章1-2分钟，根据章节数计算总时间

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
- 完整小说文件 (`./outputs/[书名]/[书名].md`)
- 元数据文件 (`./outputs/[书名]/[书名]_meta.json`)
- 章节文件目录 (`./outputs/[书名]/chapters/`)
- 工作目录 (`./outputs/[书名]/work/`)

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
    "output_dir": "./outputs",
    "reference_path": "./docs/参考资料.md"
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
    "character_state_tracker": {},  // 【新增】人物状态追踪器
    "optimized_draft": null,
    "consistency_report": null,
    "conflict_report": null
  },
  "output": null
}
```

### 人物状态追踪流程

 1. **步骤2（大纲师）**：
    - 生成 `character_initial_state`（人物初始状态）
    - 生成 `character_arc_mapping`（人物弧光映射）【新增】
    - 初始化 `character_state_history.json`（状态历史备份）【新增】

 2. **步骤4（章节写手）循环**：
    - 第1章：使用 `character_initial_state` 和 `character_arc_mapping` 作为初始输入
    - 每章写完：
      - 调用 `character-consistency-checker` 检查人物一致性【新增】
      - 提取作家输出的 `character_state_update`
      - 合并更新到 `character_state_tracker`
      - 保存到 `temp_chapter_XX_meta.json`【持久化】
      - 备份到 `character_state_history.json`【新增】
    - 下一章：从上一章的 meta 文件读取状态

 3. **步骤5-7**：人物状态用于检查逻辑一致性

 4. **步骤8（最终汇编）**：人物状态存入 `generation_log.json`

 ### 【新增】人物弧光映射使用

 - 大纲师生成每个角色在各阶段的章节范围和行为规范
 - 章节写手在写作时检查当前章节属于哪个弧光阶段
 - 作家根据弧光阶段的行为指南写作，避免禁忌行为
 - 人物一致性检查器验证人物行为是否符合弧光轨迹

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
  "output_dir": "./outputs"
}
```

**预期输出**:
- 题材: 基于参考资料创作规律的全新主题
- 书名: 符合爆款公式 + 创新元素
- 字数: 根据配置计算（默认1.2万-1.8万字）
- 章节: 根据配置（默认20章）
- 基调: 符合主题风格

---

### 示例2: 生成男频赘婿逆袭小说

**输入**:
```json
{
  "user_preference": "male",
  "output_dir": "./outputs"
}
```

**预期输出**:
- 题材: 男频-都市逆袭
- 书名: 符合爆款公式
- 字数: 根据配置（默认1.2万-1.8万字）
- 章节: 根据配置（默认20章）
- 基调: 打脸爽文

---

### 示例3: 生成全频脑洞小说

**输入**:
```json
{
  "user_preference": "all",
  "custom_topic": "摸鱼系统",
  "output_dir": "./outputs"
}
```

**预期输出**:
- 题材: 全频-脑洞
- 书名: 包含"摸鱼系统"
- 字数: 根据配置（默认1.2万-1.8万字）
- 章节: 根据配置（默认20章）
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
3. 输出到 `./outputs/` 目录
4. 显示生成进度和质量报告

### 高级使用方式

```json
{
  "user_preference": "female",
  "custom_topic": "重生复仇",
  "output_dir": "./outputs"
}
```

自定义题材或故事核，生成特定主题的小说。

---

### 示例4: 生成短视频剧本（5章，每章300字）

**输入**:
```json
{
  "user_preference": "all",
  "custom_topic": "搞笑反转",
  "output_dir": "./outputs",
  "config": {
    "target_chapters": 5,
    "chapter_word_min": 200,
    "chapter_word_max": 300
  }
}
```

**预期输出**:
- 题材: 全频-搞笑脑洞
- 书名: 包含搞笑反转元素
- 字数: 1000-1500字
- 章节: 5章章节: 5章
- 基调: 搞笑反转

---

### 示例5: 根据总字数自动计算章节数

**输入**:
```json
{
  "user_preference": "female",
  "specific_genre": "闪婚",
  "output_dir": "./outputs",
  "config": {
    "target_total_words": 5000,
    "chapter_word_min": 400,
    "chapter_word_max": 600
  }
}
```

**预期输出**:
- 章节数: 5000 ÷ 500 ≈ 10章
- 每章字数: 400-600字
- 总字数: 约5000字

---

### 示例6: 生成中篇小说（30章，每章1500字）

**输入**:
```json
{
  "user_preference": "male",
  "specific_genre": "都市逆袭",
  "output_dir": "./outputs",
  "config": {
    "target_chapters": 30,
    "chapter_word_min": 1200,
    "chapter_word_max": 1500
  }
}
```

**预期输出**:
- 字数: 3.6万-4.5万字
- 章节: 30章
- 每章字数: 1200-1500字
