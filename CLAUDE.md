# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a novel generation system using Claude Code + GLM4.7 with a multi-Skill collaboration architecture. The system automatically generates stories with flexible chapter count and word count configurations, meeting various platform standards:
- **Tomato Platform (番茄小说)**: 20 chapters, 800-1000 chars/chapter
- **Short Video Scripts**: 5-10 chapters, 200-300 chars/chapter
- **Medium Novels**: 30-50 chapters, 1000-1500 chars/chapter
- **Long Novels**: 100+ chapters, 1500-3000 chars/chapter

## Project Structure

```
short_novel/
├── docs/
│   ├── 参考资料.md          # 番茄小说爆款模板库（必读）
│   └── 技能架构优化说明.md    # 最新架构优化说明
├── skills/                  # 12 个协作 Skills
│   ├── novelist-author.md    # 作家：章节写作
│   ├── editor-reviewer.md   # 编辑：大纲/章节审核
│   ├── reader-feedback.md    # 读者：阅读体验评价
│   ├── topic-selector.md     # 选题师：题材选择
│   ├── outline-designer.md   # 大纲师：20章细纲设计
│   ├── chapter-writer.md     # 章节写手：逐章执行
│   ├── hook-crafter.md       # 钩子师：章末钩子设计
│   ├── plot-optimizer.md    . # 情节优化师：节奏优化
│   ├── tone-consistency.md   # 基调维持：风格统一
│   ├── word-count-controller.md # 字数控制：800-1000字/章
│   ├── conflict-checker.md   # 冲突检查：核心冲突贯穿
│   └── final-compiler.md    # 最终汇编：生成完整小说
├── outputs/
│   ├── novel-used-characters.json  # 全局角色跟踪文件
│   └── [书名]/                 # 每本小说的专属文件夹
│       ├── [书名].md           # 最终小说全文
│       ├── [书名]_meta.json   # 元数据和质量报告
│       ├── chapters/            # 章节分文件
│       │   ├── [书名]_chapter_01.md
│       │   ├── [书名]_chapter_02.md
│       │   └── ...
│       └── work/               # 工作数据
│           ├── outline.json      # 大纲数据
│           ├── characters.json   # 角色设定
│           └── generation_log.json # 生成日志
├── 项目需求.md              # 完整项目需求文档
└── CLAUDE.md                # 本文档
```

## Development Status

### Skills Implementation

**需要创建的 12 个 Skills**（按执行顺序）：

1. ✅ topic-selector - 选题师
2. ✅ outline-designer - 大纲师
3. ✅ editor-reviewer - 编辑审核
4. ✅ chapter-writer - 章节写手
5. ✅ novelist-author - 作家
6. ✅ hook-crafter - 钩子师
7. ✅ word-count-controller - 字数控制
8. ✅ reader-feedback - 读者反馈
9. ✅ plot-optimizer - 情节优化师
10. ✅ tone-consistency - 基调维持
11. ✅ conflict-checker - 冲突检查
12. ✅ final-compiler - 最终汇编

### Core Requirements

**Story Specifications** (Flexible Configuration):
- Total length: Configurable (default 12,000-18,000 for 20 chapters)
- Chapter count: Configurable (default 20 chapters)
- Chapter length: Configurable (default 800-1,000 characters)
- Opening: Conflict within 300 characters
- Chapter endings: Strong hooks (suspense/reversal/crisis)
- Ending: Logical closure, maximum satisfaction

**Configuration Examples**:
1. **Short Video Script**: 5-10 chapters, 200-300 chars/chapter
2. **Short Story**: 20 chapters, 800-1000 chars/chapter
3. **Medium Novel**: 30-50 chapters, 1000-1500 chars/chapter
4. **Long Novel**: 100+ chapters, 1500-3000 chars/chapter

**Quality Standards**:
- Core conflict 3 elements: Early oppression → Mid reversal → Late climax
- Compact rhythm: No filler, climax/hook in every chapter
- Character design: Decisive/independent protagonists, no saintly types
- Complete ending: Logical loop, maximum satisfaction

## Key References

1. **`docs/参考资料.md`** - 必读文档，包含：
   - 女频/男频/全频爆款题材模板
   - 书名公式（点击率提升 3 倍）
   - 20 章节奏铁律
   - 核心冲突三要素
   - 雷区规避清单
   - 20 个可直接使用的选题

2. **`项目需求.md`** - 完整 Skill 架构设计和执行流程

## Skills Execution Flow

```
用户启动（含配置：章节数、字数范围）
         → topic-selector（应用配置参数）
         → outline-designer（根据章节数分配阶段）
         → editor-reviewer
         → [chapter-writer × 章节数配置]
              ├→ novelist-author
              ├→ hook-crafter
              ├→ word-count-controller（应用字数范围）
              ├→ editor-reviewer
              └→ reader-feedback
         → plot-optimizer
         → [tone-consistency + conflict-checker] 并行
         → final-compiler（汇编可变章节数）
         → 输出完整小说
```

## Skill Design Principles

1. **角色协作**：作家、编辑、读者三个角色互相制衡，确保质量
2. **流程控制**：写作流程类 Skills 控制完整生成节奏
3. **质量保证**：质量控制类 Skills 确保符合番茄平台标准
4. **参考资料集成**：所有 Skills 必须严格遵循 `参考资料.md` 规范
5. **回溯机制**：审核不通过时能够回溯修改，而非硬重启

## Common Tasks

### 查看爆款参考模板
```bash
cat docs/参考资料.md
```

### 查看项目需求
```bash
cat 项目需求.md
```

### 查看 Skill 模板
```bash
ls skills/
cat skills/[skill-name].md
```

### 运行小说生成
待 Skills 实现后，使用 `/run-generation` 或相应命令启动完整流程

## Notes

- 所有 Skill 必须支持中文输出（番茄小说是中文平台）
- Skill 输出格式需包含结构化数据，便于后续 Skill 解析
- 参考资料中的"雷区"必须在各 Skill 中严格规避
