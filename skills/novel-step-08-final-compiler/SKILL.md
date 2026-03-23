---
name: novel-step-08-final-compiler
description: 最终汇编 - 汇总所有章节，生成完整小说文件，支持可变章节数
---

# 最终汇编

## 职责

汇总所有章节，生成完整小说文件和元数据，输出到小说专属文件夹中。支持灵活的章节数和字数配置。

## 输入格式

```json
{
  "all_chapters": [
    {
      "chapter_number": 1,
      "chapter_title": "ICU 门外",
      "chapter_content": "章节内容...",
      "word_count": 856
    },
    ...
  ],
  "outline": {
    "total_chapters": 20,
    "chapter_word_min": 800,
    "chapter_word_max": 1000,
    "chapters": [...],
    "character_arc": {...},
    "timeline": {...}
  },
  "meta": {
    "novel_title": "ICU 门外，他抱白月光笑时，我重生了",
    "category": "女频-追妻火葬场",
    "estimated_words": 18000,
    "target_chapters": 20
  },
  "quality_reports": {
    "plot_optimizer": {...},
    "tone_consistency": {...},
    "conflict_checker": {...}
  },
  "session_id": "uuid-v4",
  "creation_timestamp": "2026-03-20T10:30:00Z",
  "work_dir": "../outputs/ICU 门外，他抱白月光笑时，我重生了/",
  "reference_path": "../docs/参考资料.md"
}
```

### 输入参数说明

| 参数 | 类型 | 忌填 | 说明 |
|------|------|--------|------|
| all_chapters | array | 否 | 所有章节内容 |
| outline.total_chapters | number | 否 | 总章节数 |
| outline.chapter_word_min | number | 否 | 每章最少字数 |
| outline.chapter_word_max | number | 否 | 每章最多字数 |
| meta.novel_title | string | 否 | 小说标题 |
| meta.estimated_words | number | 否 | 预估总字数 |
| quality_reports | object | 否 | 质量报告 |

## 输出格式

### 1. 小说全文文件

输出到 `../outputs/[书名]/[书名].md`

```markdown
# ICU 门外，他抱白月光笑时，我重生了

## 作者信息

- 题材：女频-追妻火葬场
- 字数：18,000 字
- 章节：20 章
- 基调：虐恋爽感

## 人物表

- 林婉：女主（重生/觉醒）
- 陆沉：男主（渣男前夫）
- 苏月：白月光

---

## 第一章 ICU 门外

（章节内容...）

---

## 第二章 婚礼前夕

（章节内容...）

...（继续20章）
```

### 2. 元数据文件

输出到 `../outputs/[书名]/[书名]_meta.json`

### 3. 章节分文件

输出到 `../outputs/[书名]/chapters/[书名]_chapter_[章号].md`

### 4. 工作数据文件

输出到 `../outputs/[书名]/work/outline.json` (大纲数据)
输出到 `../outputs/[书名]/work/characters.json` (角色设定)
输出到 `../outputs/[书名]/work/generation_log.json` (生成日志)

```json
{
  "novel_info": {
    "title": "ICU 门外，他抱白月光笑时，我重生了",
    "category": "女频-追妻火葬场",
    "tone": "虐恋爽感",
    "creation_date": "2026-03-18",
    "word_count": 18200,
    "chapter_count": 20
  },
  "characters": {
    "protagonist": {
      "name": "林婉",
      "role": "女主",
      "arc": "重生/觉醒"
    },
    "antagonist": {
      "name": "陆沉",
      "role": "男主",
      "arc": "渣男前夫"
    },
    "secondary": [
      {"name": "苏月", "role": "白月光"}
    ]
  },
  "quality_metrics": {
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
    },
    "final_quality_score": 9.33
  },
  "output_files": {
    "novel_file": "../outputs/ICU 门外，他抱白月光笑时，我重生了/ICU 门外，他抱白月光笑时，我重生了.md",
    "meta_file": "../outputs/ICU 门外，他抱白月光笑时，我重生了/ICU 门外，他抱白月光笑时，我重生了_meta.json",
    "chapter_dir": "../outputs/ICU 门外，他抱白月光笑时，我重生了/chapters/",
    "work_dir": "../outputs/ICU 门外，他抱白月光笑时，我重生了/work/"
  }
}
```

## 汇编流程

### 1. 生成小说全文

```markdown
# 书名

## 基本信息
- 题材
- 字数（根据配置）
- 章节（根据配置）
- 基调

## 人物表
- 主要人物列表

## 分隔线（---）

## 第X章 章节标题
（章节内容）

...（继续所有章节，根据 total_chapters）
```

### 2. 汇总元数据

```json
{
  "novel_info": {...},      // 小说基本信息
  "characters": {...},     // 人物设定
  "quality_metrics": {...}, // 质量指标
  "final_quality_score": X, // 最终评分
  "output_files": {...}    // 输出文件路径
}
```

### 3. 计算最终评分

```
最终评分 = (情节优化评分 + 基调一致性评分 + 冲突检查评分) / 3
```

评分标准：
- 9.0+：优秀，可直接发布
- 8.0-8.9：良好，建议微调后发布
- 7.0-7.9：一般，建议优化后发布
- <7.0：较差，建议重新生成

### 4. 字数和章节数统计

根据实际输出统计：
- 实际总字数：sum(chapters.word_count)
- 实际章节数：all_chapters.length
- 平均每章字数：actual_words / actual_chapters

## 文件输出

### 输出目录

```
../outputs/
└── ICU 门外，他抱白月光笑时，我重生了/
    ├── ICU 门外，他抱白月光笑时，我重生了.md  # 完整小说
    ├── ICU 门外，他抱白月光笑时，我重生了_meta.json  # 元数据
    ├── chapters/  # 章节分文件
    │   ├── ICU 门外，他抱白月光笑时，我重生了_chapter_01.md
    │   ├── ICU 门外，他抱白月光笑时，我重生了_chapter_02.md
    │   └── ...
    └── work/  # 工作数据
        ├── outline.json
        ├── characters.json
        └── generation_log.json
```

### 输出检查清单

| 检查项 | 要求 |
|--------|------|
| 小说文件 | 包含完整所有章节内容（根据配置的章节数） |
| 元数据文件 | 包含所有质量指标 |
| 文件编码 | UTF-8 |
| 文件格式 | Markdown / JSON |
| 输出路径 | 正确的 outputs/ 目录 |
| 字数统计 | 与配置的范围一致 |
| 章节数统计 | 与配置的章节数一致 |

## 验证方法

### 1. 文件完整性验证
```bash
# 检查文件是否存在
ls ../outputs/[书名].md
ls ../outputs/[书名]_meta.json
```

### 2. 内容验证
```bash
# 检查章节数量
grep "^## 第" ../outputs/[书名].md | wc -l
# 应该输出 20

# 检查字数（近似）
wc -m ../outputs/[书名].md
```

### 3. 元数据验证
```bash
# 检查 JSON 格式
cat ../outputs/[书名]_meta.json | jq .
```

## 注意事项

1. 输出文件名使用书名，避免特殊字符
2. 文件编码必须是 UTF-8
3. 章节分隔清晰
4. 元数据完整准确
5. 确保输出到正确的目录
6. 支持可变章节数（不再固定为20章）
7. 根据配置的字数范围进行统计和验证
